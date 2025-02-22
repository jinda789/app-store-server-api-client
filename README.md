# App Store Server API Client

Unoffical **PHP** client for [App Store Server API](https://developer.apple.com/documentation/appstoreserverapi).

## Requirements

- PHP >= 7.2

## Installation

```
composer require jinda789/app-store-server-api-client
```

## Usage

Create an API Key instance:

```php
$apiKey = new class extends AbstractApiKey {};
$apiKey->setPrivateKey(\file_get_contents($privateKeyFile));
$apiKey->setPrivateKeyId('Your private key id');
$apiKey->setIssuerId('Your issuer id');
$apiKey->setName('Key name (optional)');
```

Create a Bundle instance(s):

```php
$bundle = new class extends AbstractBundle {};
$bundle->setBundleId('Your bundle id');
$bundle->setName('Bundle name (optional)');

$bundle2 = new class extends AbstractBundle {};
$bundle2->setBundleId('Your bundle #2 id');
```

Create an API client instance:

```php
$client = new AppStoreServerApiClient($apiKey, $bundle, Environment::PRODUCTION);
```

Use client for one or multiple bundles:

```php
try {
    $historyResponse = $client->getTransactionHistory($originalTransactionId);
    $transactions = $historyResponse->getDecodedTransactions();
} catch (HttpClientException $httpClientException) {
    echo "HTTP client error: " . $httpClientException->getMessage();
} catch (AppleException $appleException) {
    echo "Apple error ({$appleException->getCode()}): " . $appleException->getMessage();
}

try {
    $bundle2HistoryResponse = $client->setBundle($bundle2)->getTransactionHistory($originalTransactionId);
} catch (\Exception $e) {
    echo "Error: " . $e->getMessage();
}
```

## Methods

### AppStoreServerApiClient::getTransactionHistory

```php
AppStoreServerApiClient::getTransactionHistory(string $originalTransactionId, ?string $revision = null): HistoryResponse
```

Get a customer’s in-app purchase transaction history for your app.

https://developer.apple.com/documentation/appstoreserverapi/get_transaction_history

### AppStoreServerApiClient::getAllTransactionHistory

```php
AppStoreServerApiClient::getAllTransactionHistory(string $originalTransactionId): JWSTransactionDecodedPayload[]
```

Recursively get FULL transaction history.

### AppStoreServerApiClient::getAllSubscriptionStatuses

```php
AppStoreServerApiClient::getAllSubscriptionStatuses(string $originalTransactionId): StatusResponse
```

Get the statuses for all of a customer’s subscriptions in your app.

https://developer.apple.com/documentation/appstoreserverapi/get_all_subscription_statuses

### AppStoreServerApiClient::sendConsumptionInformation

```php
AppStoreServerApiClient::sendConsumptionInformation(string $originalTransactionId, ConsumptionRequest $request): void
```

Send consumption information about a consumable in-app purchase to the App Store after your server receives a consumption request notification.

https://developer.apple.com/documentation/appstoreserverapi/send_consumption_information

### AppStoreServerApiClient::lookUpOrderId

```php
AppStoreServerApiClient::lookUpOrderId(string $orderId): OrderLookupResponse
```

Get a customer’s in-app purchases from a receipt using the order ID.

https://developer.apple.com/documentation/appstoreserverapi/look_up_order_id

### AppStoreServerApiClient::getRefundHistory

```php
AppStoreServerApiClient::getRefundHistory(string $originalTransactionId): RefundLookupResponse
```

Get a list of all refunded in-app purchases in your app for a customer.

https://developer.apple.com/documentation/appstoreserverapi/get_refund_history

### AppStoreServerApiClient::extendSubscriptionRenewalDate

```php
AppStoreServerApiClient::extendSubscriptionRenewalDate(
    string $originalTransactionId,
    ExtendRenewalDateRequest $request
): ExtendRenewalDateResponse
```

Extend the renewal date of a customer’s active subscription using the original transaction identifier.

https://developer.apple.com/documentation/appstoreserverapi/extend_a_subscription_renewal_date

### AppStoreServerApiClient::setBundle

```php
AppStoreServerApiClient::setBundle(BundleInterface $bundle): self
```

Set App Store bundle for authorize your API calls.

### AppStoreServerApiClient::setTokenTtl

```php
AppStoreServerApiClient::setTokenTtl(int $ttl): self
```

Set new value for JWT TTL (in seconds). Maximum value: 3600.

https://developer.apple.com/documentation/appstoreserverapi/generating_tokens_for_api_requests

### AppStoreServerApiClient::setHttpClientRequestTimeout

```php
AppStoreServerApiClient::setHttpClientRequestTimeout(float $timeout): self
```

Set new value for HTTP client request timeout (in seconds).

## Receiving App Store Server Notifications V2

To receive [App Store Server Notifications](https://developer.apple.com/documentation/appstoreservernotifications)
use **AppStoreServerNotificationsClient**:

```php
$notificationClient = new AppStoreServerNotificationsClient();

try {
    $payload = $notificationClient->receive($requestBody);
} catch (NotificationBadRequestException $exception) {
    echo $exception->getMessage();
}
```

Note that AppStoreServerNotificationsClient only for version 2 notifications.
