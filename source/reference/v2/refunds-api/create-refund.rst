Create payment refund
=====================
.. api-name:: Refunds API
   :version: 2

.. endpoint::
   :method: POST
   :url: https://api.mollie.com/v2/payments/*id*/refunds

.. authentication::
   :api_keys: true
   :organization_access_tokens: true
   :oauth: true

Creates a :doc:`Refund </payments/refunds>` on the Payment. The refunded amount is credited to your customer.

Parameters
----------
Replace ``id`` in the endpoint URL by the payment's ID, for example ``v2/payments/tr_7UhSN1zuXS/refunds``.

.. list-table::
   :widths: auto

   * - ``amount``

       .. type:: amount object
          :required: true

     - The amount to refund. For some payments, it can be up to €25.00 more than the original transaction amount.

       .. list-table::
          :widths: auto

          * - ``currency``

              .. type:: string
                 :required: true

            - An `ISO 4217 <https://en.wikipedia.org/wiki/ISO_4217>`_ currency code. The currency must be the same as
              the corresponding payment.

          * - ``value``

              .. type:: string
                 :required: true

            - A string containing the exact amount you want to refund in the given currency. Make sure to send the right
              amount of decimals. Non-string values are not accepted.

   * - ``description``

       .. type:: string
          :required: false

     - The description of the refund you are creating. This will be shown to the consumer on their card or
       bank statement when possible. Max. 140 characters.

   * - ``metadata``

       .. type:: mixed
          :required: false

     - Provide any data you like, for example a string or a JSON object. We will save the data alongside the
       refund. Whenever you fetch the refund with our API, we will also include the metadata. You can use up to
       approximately 1kB.

Access token parameters
^^^^^^^^^^^^^^^^^^^^^^^
If you are using :doc:`organization access tokens </guides/authentication>` or are creating an
:doc:`OAuth app </oauth/overview>`, you can enable test mode through the ``testmode`` parameter.

.. list-table::
   :widths: auto

   * - ``testmode``

       .. type:: boolean
          :required: false

     - Set this to ``true`` to refund a test mode payment.

Mollie Connect parameters
^^^^^^^^^^^^^^^^^^^^^^^^^
With Mollie Connect you can split payments that are processed through your app across multiple connected accounts. When
creating refunds for those split payments, you can use the ``routingReversal`` parameter to pull the split payment back
to the platform balance. To learn more about creating refunds for split payments, please refer to the
:doc:`Splitting payments guide </oauth/splitting-payments>`.

.. list-table::
   :widths: auto

   * - ``reverseRouting``

       .. type:: boolean
          :required: false

     - A shortcut for setting the ``routingReversal`` property to match the routing of the original payment. For
       example, if a €10,00 payment got split by sending €2,50 to the platform and €7,50 to the connected account, then
       setting this parameter to ``true`` will pull back the €7,50 from the connected account.

   * - ``routingReversal``

       .. type:: array
          :required: false

     - An optional routing configuration which enables you to 'reverse the routing' that was performed for the original
       payment.

       See the :doc:`Splitting payments guide </oauth/splitting-payments>` guide and the ``routing`` parameter on the
       :doc:`Create payment endpoint </reference/v2/payments-api/create-payment>` for more information on payment
       routing.

       If a routing reversal array is supplied, it must contain objects with the following parameters:

       .. list-table::
          :widths: auto

          * - ``amount``

              .. type:: amount object
                 :required: false

            - The amount to pull back from the connected account to the platform account. If omitted, the full amount
              that was sent to the connected account will be pulled back.

              .. list-table::
                 :widths: auto

                 * - ``currency``

                     .. type:: string
                        :required: true

                   - An `ISO 4217 <https://en.wikipedia.org/wiki/ISO_4217>`_ currency code. Currently only ``EUR``
                     payments can be routed.

                 * - ``value``

                     .. type:: string
                        :required: true

                   - A string containing the exact amount to be pulled back in the given currency. Make sure to send the
                     right amount of decimals. Non-string values are not accepted.

          * - ``fromOrganizationId``

              .. type:: string
                 :required: true

            - The ID of the organization that the money needs to be pulled back from.

Response
--------
``201`` ``application/hal+json``

A refund object is returned, as described in :doc:`Get payment refund </reference/v2/refunds-api/get-refund>`.

Example
-------

.. code-block-selector::
   .. code-block:: bash
      :linenos:

      curl -X POST https://api.mollie.com/v2/payments/tr_WDqYK6vllg/refunds \
         -H "Authorization: Bearer test_dHar4XY7LxsDOtmnkVtjNVWXLSlXsM" \
         -d "amount[currency]=EUR" \
         -d "amount[value]=5.95" \
         -d "metadata={\"bookkeeping_id\": 12345}"

   .. code-block:: php
      :linenos:

      <?php
      $mollie = new \Mollie\Api\MollieApiClient();
      $mollie->setApiKey("test_dHar4XY7LxsDOtmnkVtjNVWXLSlXsM");

      $payment = $mollie->payments->get("tr_WDqYK6vllg");
      $refund = $payment->refund([
      "amount" => [
         "currency" => "EUR",
         "value" => "5.95" // You must send the correct number of decimals, thus we enforce the use of strings
      ]
      ]);

   .. code-block:: python
      :linenos:

      from mollie.api.client import Client

      mollie_client = Client()
      mollie_client.set_api_key('test_dHar4XY7LxsDOtmnkVtjNVWXLSlXsM')

      payment = mollie_client.payments.get('tr_WDqYK6vllg')
      refund = mollie_client.payment_refunds.on(payment).create({
         'amount': {
               'value': '5.95',
               'currency': 'EUR'
         }
      })

   .. code-block:: ruby
      :linenos:

      require 'mollie-api-ruby'

      Mollie::Client.configure do |config|
        config.api_key = 'test_dHar4XY7LxsDOtmnkVtjNVWXLSlXsM'
      end

      refund = Mollie::Payment::Refund.create(
        payment_id: 'tr_WDqYK6vllg',
        amount:      { value: '5.00', currency: 'EUR' },
        description: 'Example refund description'
      )

   .. code-block:: javascript
      :linenos:

      const { createMollieClient } = require('@mollie/api-client');
      const mollieClient = createMollieClient({ apiKey: 'test_dHar4XY7LxsDOtmnkVtjNVWXLSlXsM' });

      (async () => {
        const refund = await mollieClient.payments_refunds.create({
          paymentId: 'tr_WDqYK6vllg',
          amount: {
            value: '5.95',
            currency: 'EUR',
          },
        });
      })();

Response
^^^^^^^^
.. code-block:: none
   :linenos:

   HTTP/1.1 201 Created
   Content-Type: application/hal+json

   {
       "resource": "refund",
       "id": "re_4qqhO89gsT",
       "amount": {
           "currency": "EUR",
           "value": "5.95"
       },
       "status": "pending",
       "createdAt": "2018-03-14T17:09:02.0Z",
       "description": "Order #33",
       "metadata": {
            "bookkeeping_id": 12345
       },
       "paymentId": "tr_WDqYK6vllg",
       "_links": {
           "self": {
               "href": "https://api.mollie.com/v2/payments/tr_WDqYK6vllg/refunds/re_4qqhO89gsT",
               "type": "application/hal+json"
           },
           "payment": {
               "href": "https://api.mollie.com/v2/payments/tr_WDqYK6vllg",
               "type": "application/hal+json"
           },
           "documentation": {
               "href": "https://docs.mollie.com/reference/v2/refunds-api/create-refund",
               "type": "text/html"
           }
       }
   }

Response (duplicate refund detected)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: none
   :linenos:

   HTTP/1.1 409 Conflict
   Content-Type: application/hal+json

    {
        "status": 409,
        "title": "Conflict",
        "detail": "A duplicate refund has been detected",
        "_links": {
            "documentation": {
                "href": "https://docs.mollie.com/guides/handling-errors",
                "type": "text/html"
            }
        }
    }
