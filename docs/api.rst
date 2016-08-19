Interface
===============

Attestations - /attestations
--------------------------------------------

When member created the template on Baoquan.com, he/she/it can send data, which is required by consummating the template, through this interface.

payload
^^^^^^^^^^^^^^^

=================  ==================================================================  ==================================
 Parameter name     	Description                                                     Mandatory/Optional
=================  ==================================================================  ==================================
 unique_id			String, the unique id of attestation, length < 255					Mandatory
 template_id        String, the ID of template                                      	Mandatory
 identities         Object, item for identities                                     	Mandatory
 factoids           Array, factoids set                                             	Optional
 completed          Boolean value, whether factoids set is uploaded successfully     	Optional, default is true
 attachments        Array, information object of attachments, optional                  Optional
=================  ==================================================================  ==================================

unique_id is the unique code of attestation, which prevent from repeat upload same attestation cause by network timeout or other exceptions. If an attestation uploads several times, we just save once, and return the same attestion id.

**Factoid** is an Object, which contains two fields: “type”, “data”, for example::

	{
		"unique_id": "9de7be94-a697-4398-945a-678d3f916b9f",
		"type": "hash",
		"data": {
			"userName": "David Smith",
			"idCard": "42012319800127691X"
		}
	}

The "unique_id" of factoid is similar to the "unique_id" of attestation. If the same factoid contents upload several times at the one attestation, we just handle once.

The “type” is the user defined name of factoids, “data” is the field value of factoids, as following：

.. image:: images/use_template.png

The template contains two factoids: “common” and ”hash”. attestation_no, attestation_at, product_name, organization_name in the “common” are provided by Baoquan.com when you are consummating the template. “hash” is defined by members selves, therefore, ,members need to upload that through API.

Template could contain multiple customisable factoids, for example, factoA and factoB. Members could choose to upload twice separately: upload factoA first and set “completed” to false, and then upload factoB again, set “completed” to true.

.. note:: Once “completed” has been set to true, no more factoids uploading will be accepted.

Assume payload is as below::

	{
		"unique_id": "acafa00d-5579-4fe5-93c1-de89ec82006e",
		"template_id": "2hSWTZ4oqVEJKAmK2RiyT4",
		"identities": {
			"MO": "15857112383",
			"ID": "42012319800127691X"
		},
		"factoids": [
			{
				"unique_id": "9de7be94-a697-4398-945a-678d3f916b9f",
				"type": "hash",
				"data": {
					"userName": "Richard Hammond",
					"idCard": "330124199501017791",
					"buyAmount": 0.3,
					"incomeStartTime": "2015-12-02",
					"incomeEndTime": "2016-01-01",
					"createTime": "2015-12-01 14:33:44",
					"payTime": "2015-12-01 14:33:59",
					"payAmount": 600
				}
			}
		],
		"completed": true
	}

After Attestations, you can check the consummated content on Baoquan.com by reference number. As shown below:

.. image:: images/render_template.png

Attachments
^^^^^^^^^^^^^^^

When you uploading the factoids data, you can upload attachments related to the factoids simultaneously. In payload, the “attachments” store the check code of attachments.

Upload a single attachment as “form” data::

	<form method='post' enctype='multipart/form-data'>
	  ...
	  <input type=file name="attachments[0][]">
	</form>

	payload = {
		"unique_id": "...",
		"template_id": "...",
		"identities": {...},
		"factoids": [
			{
				"unique_id": "...",
				"type": "...",
				"data": {...}
			}
		],
		"completed": true,
		"attachments": {
			"0": ["checkSum"]
		}
	}

Upload multiple attachments as “form” data::

	<form method='post' enctype='multipart/form-data'>
	  ...
	  <input type=file name="attachments[0][]">
	  <input type=file name="attachments[0][]">
	  <input type=file name="attachments[1][]">
	</form>

	payload = {
		"unique_id": "...",
		"template_id": "...",
		"identities": {...},
		"factoids": [
			{
				"unique_id": "...",
				"type": "...",
				"data": {...}
			},
			{
				"unique_id": "...",
				"type": "...",
				"data": {...}
			}
		],
		"completed": true,
		"attachments": {
			"0": [
				"checkSum1", 
				{
					"checksum": "checkSum2",
					"sign": {
						"F98F99A554E944B6996882E8A68C60B2": ["Party A (signature)", "Party A juristische Person (signature)"],
						"0A68783469E04CAC95ADEAE995A92E65": ["Party B (signature)"]
					}
				}
			],
			"1": ["checkSum3"]
		}
	}

The “key” of "attachments" is referring as the superscript of the factoids array， for example, "0" correspond with factoids[0]. The "value" of "attachments" is an array, each element correspond with attachment information.

There are two kinds of attachment informations: checksum and sign information, and you must offer checksum. While attachment information contain checksum, you can use String object; While attachment information contain sign  information, you need to use object instead.

.. note:: only pdf attachment can make sign.

The “checksum” is generated from file by SHA256, take Java as an example::

	String file = "/path/to/file";
	InputStream in = new FileInputStream(new File(file));

	// Use SHA256 to hash the file
	bytes[] digestBytes = DigestUtils.getDigest("SHA256").digest(StreamUtils.copyToByteArray(in));

	// Transform bytes into hexadecimal
	String checkSum = Hex.encodeHexString(digestBytes);

Sign is an object, the "key" is caId (when member apply Certification, it will return caId), the "value" is the sign keyword array. For example, Richard and Edward would like to sign at "xxx.pdf" paper, Richard call ca interface to get the caId is "F98F99A554E944B6996882E8A68C60B2", Edward call ca interface to get the caId is "0A68783469E04CAC95ADEAE995A92E65", then Richard need to sign at "Party A (signature)" and "Party A juristische Person (signature)", and Edward need to sign at "Party B (signature)", so the sign object is like following::

	"sign": {
		"F98F99A554E944B6996882E8A68C60B2": ["Party A (signature)", "Party A juristische Person (signature)"],
		"0A68783469E04CAC95ADEAE995A92E65": ["Party B (signature)"]
	}

.. note:: One user can sign at different sign channel, but keyword should be keep the same, and can't repeat with the body part.

Returned data
^^^^^^^^^^^^^^

When Attestation interface is requested successfully, the reference number will be returned.

=================  ================================================================
Field name 		   Description                            
=================  ================================================================
no                 String, reference number of the attestation                                        
=================  ================================================================

For example::

	{
		"request_id": "2XiTgZ2oVrBgGqKQ1ruCKh",
		"data": {
			"no": "rBgGqKQ1ruCKhXiTgZ2oVr",
		}
	}

Add factoids - /factoids
--------------------------------------------

Members could use Factoids interface to upload more factoids set.

payload
^^^^^^^^^^^^^^^

=================  ================================================================ ================================
Parameter name 	   Description                                                      Mandatory/Optional
=================  ================================================================ ================================
ano                String, the ID of attestation                                    Mandatory
factoids           Array, factoids set                                              Mandatory
completed          Boolean value, whether factoids set is uploaded successfully     Optional, default is true
attachments        Array, checksum code of attachments, optional                    Optional
=================  ================================================================ ================================

For example::

	{
		"ano": "2hSWTZ4oqVEJKAmK2RiyT4",
		"factoids": [
			{
				"unique_id": "9de7be94-a697-4398-945a-678d3f916b9f",
				"type": "hash",
				"data": {
					"userName": "Edward Snow",
					"idCard": "330124199501017791",
					"buyAmount": 0.3,
					"incomeStartTime": "2015-12-02",
					"incomeEndTime": "2016-01-01",
					"createTime": "2015-12-01 14:33:44",
					"payTime": "2015-12-01 14:33:59",
					"payAmount": 600
				}
			}
		],
		"completed": false
	}

Returned data
^^^^^^^^^^^^^^

=================  ================================================================
Field name         Description                            
=================  ================================================================
success            Boolean, whether successful or not                                           
=================  ================================================================

For example::

	{
		"request_id": "2XiTgZ2oVrBgGqKQ1ruCKh",
		"data": {
			"success": true,
		}
	}

Get attestation data - /attestation
--------------------------------------

Member can get get the upload attestation data through this API, such as identities, factoids, etc.

payload
^^^^^^^^^^^^^^^

=================  ============================================  ===============================
Parameter name 			Description                       							Mandatory/Optional
=================  ============================================  ===============================
ano                String, reference number of the attestation      Mandatory
fields             Array, returned fields              			        Optional, default is true
=================  ============================================  ===============================

Get attestation data such as identities, factoids, attachments should take a long time to access database and decode data, so you can choose which fields you want to return.

Returned data
^^^^^^^^^^^^^^

=================  ================================================================
Parameter name 	   Description                                   
=================  ================================================================
no                 Reference number of the attestation
template_id        The ID of template
identities         Identities
factoids           List of factoids
completed          Return true if factoids is uploaded, or return fasle.
attachments        List of attachments
blockchain_hash    The hash of blockchain, return null if it doesn't link to blockchain yet
=================  ================================================================

attachments is an array, which The "key" of "attachments" is referring as the superscript of the factoids array, and the "value" is an attachment array.

（1）When fields set null, we will get all data, the result is as below::

	{
		"request_id": "2XiTgZ2oVrBgGqKQ1ruCKh",
		"data": {
			"no": "DB0C8DB14E3C44C7B9FBBE30EB179241",
			"unique_id": "acafa00d-5579-4fe5-93c1-de89ec82006e",
			"template_id" : "5Yhus2mVSMnQRXobRJCYgt",
			"identities": {
				"ID": "42012319800127691X",
				"MO": "15857112383"
			},
			"factoids": [
				{
					"unique_id": "28fcdf56-bff3-4ed9-9f87-c8d35ad49e0c",
					"type": "product",
					"data": {
						"name:: "zjmax",
						"description": "p2g financing platform""
					}
				},
				{
					"unique_id": "e68eb8bc-3d7a-4e22-be47-d7999fb40c9a",
					"type": "user",
					"data": {
						"name": "David Smith",
						"phone_number": "13234568732",
						"registered_at": "1466674609",
						"username": "tom"
					}
				}
			],
			"completed": true,
			"attachments": {
				"1": [
					"2EHJQPs5j4SZpEKQXQ7r6C",
					"2F81ZJXosNjzrPJsXKywAu"
				]
			},
			"blockchain_hash": "s5j4SZpEKQXQ7r6C2F81ZJXosNjzrPJsXKywAu"
		}
	}

（2）When fields set an empty array, it doesn't return the values of identities, factoids and attachments, the result is as below::
	
	{
		"request_id": "2XiTgZ2oVrBgGqKQ1ruCKh",
		"data": {
			"no": "DB0C8DB14E3C44C7B9FBBE30EB179241",
			"unique_id": "acafa00d-5579-4fe5-93c1-de89ec82006e",
			"template_id" : "5Yhus2mVSMnQRXobRJCYgt",
			"identities": null,
			"factoids": null,
			"completed": true,
			"attachments": null,
			"blockchain_hash": "s5j4SZpEKQXQ7r6C2F81ZJXosNjzrPJsXKywAu"
		}
	}

So when you want to get the hash of blockchain immediately, it's best way to set field as an empty array.

（3）When fields set an array, such as ["identities"], the result is as below::

	{
		"request_id": "2XiTgZ2oVrBgGqKQ1ruCKh",
		"data": {
			"no": "DB0C8DB14E3C44C7B9FBBE30EB179241",
			"unique_id": "acafa00d-5579-4fe5-93c1-de89ec82006e",
			"template_id" : "5Yhus2mVSMnQRXobRJCYgt",
			"identities": {
				"ID": "42012319800127691X",
				"MO": "15857112383"
			},
			"factoids": null,
			"completed": true,
			"attachments": null,
			"blockchain_hash": "s5j4SZpEKQXQ7r6C2F81ZJXosNjzrPJsXKywAu"
		}
	}

Download the attestation file - /attestation/download
--------------------------------------------------------------

When member upload data to Baoquan, we should take some certain process(rendered by template) to create a Baoquan file. a Baoquan file will be eventually hashed to the blockchain, so it can eventually make a notarised certificate at notary office or make a judicial report at judicial evaluation center.

payload
^^^^^^^^^^^^^^^

=================  ===============================================  ===============================
Parameter name 				Description        									 						Mandatory/Optional
=================  ===============================================  ===============================
ano                String, reference number of the attestation  			Mandatory
=================  ===============================================  ===============================

Returned file
^^^^^^^^^^^^^^^

This interface will get the Baoquan file and filename. The file is body of the result of http message, and the filename is header of http message. Content=Disposition is the header name. The value of header is like below::
	
	form-data; name=Content-Disposition; filename=5Yhus2mVSMnQRXobRJCYgt.zip

As Java for example::

	// ommit using apache http client to create http request
	// closeableHttpResponse is an instance of CloseableHttpResponse
	HttpEntity httpEntity = closeableHttpResponse.getEntity();
	Header header = closeableHttpResponse.getFirstHeader(MIME.CONTENT_DISPOSITION);
	Pattern pattern = Pattern.compile(".*filename=\"(.*)\".*");
	Matcher matcher = pattern.matcher(header.getValue());
	String fileName = "";
	if (matcher.matches()) {
		fileName = matcher.group(1);
	}
	FileOutputStream fileOutputStream = new FileOutputStream(fileName);
	IOUtils.copy(httpEntity.getContent(), fileOutputStream)


Apply for Certification - /cas
--------------------------------------------------------------

If the attachment of attestation need to sign, you must apply for Certification.

payload
^^^^^^^^^^^^^^^

=================  =========================================================== =========================================
Parameter name 		 Description                                 				Mandatory/Optional
=================  =========================================================== =========================================
type               String，user type：PERSONAL、ENTERPRISE                		Mandatory
name               enterprise name                                       		Mandatory when user type is ENTERPRISE
ic_code            enterprise Register Code or Unified Social Credit Code   	Mandatory when user type is ENTERPRISE
org_code           enterprise oragnization code                        			Mandatory when user type is ENTERPRISE
tax_code           enterprise tax code                                 			Mandatory when user type is ENTERPRISE
link_name          user name or enterprise contact user name             		Mandatory
link_id_card       user ID card or enterprise contact user ID card         		Mandatory
link_phone         user phone number or enterprise contact user phone number    Mandatory
link_email         user mail or enterprise contact user mail                	Mandatory
=================  =========================================================== =========================================

Apply for personal certification::
	
	{
		"type": "PERSONAL",
		"link_name": "Richard Hammond",
		"link_id_card": "330184198501184115",
		"link_phone": "13378784545",
		"link_email": "123@qq.com"
	}

If enterprise has "three in one" situation, you should use Unified Social Credit Code::
	
	{
		"type": "ENTERPRISE",
		"name": "xxx Co., Ltd.",
		"ic_code": "91332406MA27XMXJ27",
		"link_name": "Richard Hammond",
		"link_id_card": "330184198501184115",
		"link_phone": "13378784545",
		"link_email": "123@qq.com"
	}

If not, then use business registration code, organization code, tax code to apply for certification::

	{
		"type": "ENTERPRISE",
		"name": "xxx Co., Ltd.",
		"ic_code": "419001000033792",
		"org_code": "177470403",
		"tax_code": "419001177470403",
		"link_name": "Richard Hammond",
		"link_id_card": "330184198501184115",
		"link_phone": "13378784545",
		"link_email": "123@qq.com"
	}

returned data
^^^^^^^^^^^^^^

=================  ================================
Description 		Description                            
=================  ================================
no                  caId                                    
=================  ================================

For example::

	{
		"request_id": "2XiTgZ2oVrBgGqKQ1ruCKh",
		"data": {
			"no": "F98F99A554E944B6996882E8A68C60B2",
		}
	}