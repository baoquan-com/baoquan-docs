Request
==============
The accessing of Baoquan.com API requires https protocol, and digital signature.


Path
--------------

The accessing of Baoquan.com API are defined as Sandbox environment and Formal environment. A template edited under the Formal environment could be used in the Sandbox environment, however, the data in Sandbox environment will be deleted regularly.

Baoquan.com will provide API with backward compatibility, different API versions will be denoted as strings as v1, v2.1 etc. The current version is v1.

Formal environment: `https://baoquan.com/api/v1`

Sandbox environment: `https://sandbox.baoquan.com/api/v1`

Request path = API environment + Interface name, for example: The interface of Attestation is /attestations, therefore, the request path in Formal environment is `https://baoquan.com/api/v1/attestations`


Method
--------------

All requests are HTTP POST method.

Parameters
--------------

.. _Unix Time: https://en.wikipedia.org/wiki/Unix_time>

=================  =====================================================================================
Parameter name 	   Description 
=================  =====================================================================================
request_id         The id of request. This is the unique string created by accessed Member. The string length is no longer than 32 bits.
access_key         The access identifier. When you successfully uploaded a RSA public key you will receive an access_key
tonce              Request time, must be sent in `Unix Time`_ format. The difference between Tonce and server time could not beyond ±300 seconds.
payload            Signed part of data body, compiled under JSON, different request normally have different payload data.
signature          Strings singed by your RSA private key, detailed signature method will be described further later.
=================  ===================================================================================== 

For example::

	{
		"request_id": "2XiTgZ2oVrBgGqKQ1ruCKh",
		"access_key": "2y7cg8kmoGDrDBXJLaizoD",
		"tonce": 1464594744,
		"payload": {
			"template_id": "2hSWTZ4oqVEJKAmK2RiyT4",
		}
		"signature": "moGDrDBXJLaizoD2hSWTZ4oqVEJKAmK2RiyT4"
	}