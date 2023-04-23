# Errors

The 1BitPay API uses the following error codes:

<div style="width:80px">Error Code</div> | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your API key is wrong.
403 | Forbidden -- The kitten requested is hidden for administrators only.
404 | Not Found -- The specified kitten could not be found.
405 | Method Not Allowed -- You tried to access a kitten with an invalid method.
406 | Not Acceptable -- You requested a format that isn't json.
410 | Gone -- The kitten requested has been removed from our servers.
418 | I'm a teapot.
429 | Too Many Requests -- You're requesting too many kittens! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.

50000| Real name is not allowed to be empty!
50001| The API capability ID cannot be empty!
50002| Cell phone area code is not allowed to be empty!
50003| Mobile phone number is not allowed to be empty!
50004| Synchronous callback address is not allowed to be empty!
50005| Asynchronous callback address is not allowed to be empty!
50006| Both digital currency amount and fiat currency amount are not allowed to be empty!
50007| Digital currency type is not allowed to be empty!
50008| Fiat currency is not allowed to be empty!
50009| Certificate is not allowed to be empty!
50010| The document type is not allowed to be empty!
50011| KYC level is not allowed to be empty!
50012| Merchant ID is not allowed to be empty!
50013| Signature type is not allowed to be empty!
50014| Signature is not allowed to be empty!
50015| Merchant order number is not allowed to be empty!
50016| The order type cannot be empty
50017| apiKey is not allowed to be empty
150017| Incorrect order type
150018| The certificate type is incorrect
150019| Incorrect encryption rules
150020| The API capability ID does not exist
150021| The merchant has not activated this API capability, please contact customer service!
150022| The phone number is incorrect
150023| Incorrect KYC level
150024| Merchant API_KEY information does not exist
150025| Incorrect request signature
150026| Digital currency does not exist
150027| Digital currency has been banned from buying and selling coins
150028| Digital currency has been banned from buying coins
150029| Digital currency has been banned from selling coins
150030| Signature exception
150031| Trading currency pair does not exist
150031| The transaction volume is higher than the maximum transaction volume limit
150032| The transaction volume is lower than the minimum transaction volume limit
150033| Payment in fiat is disabled
150034| Payment fiat does not exist
150035| The customer has been disabled and cannot OTC
150036| The client has been blacklisted and cannot be OTC
50037| Cashier has expired
50038| Order creation failed, please contact the platform for processing
50039| Customer information does not exist
50040| Order fetching exception, please try again later
50041| The order status is incorrect, please contact the platform for processing
50042| The apiKey is incorrect