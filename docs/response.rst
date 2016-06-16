Response
==============

The response of Baoquan.com API is a JSON string. Normal response of HTTP Status Code will be 2xx, except that are error response.

Normal response
------------------

Normal response value will be shown as blow:

===========  ================================================================
Field name   Description 
===========  ================================================================
request_id   The ID of member’s request
data         Valid data, details will be described within interface.  
===========  ================================================================

For example::

	{
		"request_id": "2XiTgZ2oVrBgGqKQ1ruCKh",
		"data": {
			"attestation_id": "nXiTrBgQ1ruCKhgZ2oV"
		}
	} 


Error response
---------------------

Error response will be shown as blow：

===========  ================================================================ 
Field name   Description 
===========  ================================================================ 
request_id   The ID of member’s request 
message      Error causes
timestamp    Timestamp, it has millisecond level accuracy
===========  ================================================================

Error HTTP Status Code

===========  ====================================================================================
Code         Description 
===========  ==================================================================================== 
400          Request error (eg. Parameter not matched with format requirement) 
404          Request path not existed
405          Request method error
413          Data size requested too large (eg. File uploaded too large on size)
429          Request amount beyond limitation
500          Request failed (eg. Digital signature verification failure)   
===========  ====================================================================================

For example::

	{
		"request_id": "2XiTgZ2oVrBgGqKQ1ruCKh",
		"message": "数字签名校验失败",
		"timestamp": 1464672264000
	} 