Python
=================

If you use pip, you can add dependency at requirements.txt as below::

	eagle-sdk==1.0.8

.. note:: This SDK should be running at Python 3

Create Baoquan Client
-----------------------

::

	from baoquan import BaoquanClient

	client = BaoquanClient()
	// set API address, like Baoquan test environment
	client.host = 'https://baoquan.com'
	// set access key
	client.access_key = 'fsBswNzfECKZH9aWyh47fc'
	// set absolute path of rsa private key file
	client.pem_path = 'path/to/rsa_private.pem'

rsa private key should begin with **-----BEGIN RSA PRIVATE KEY-----** and end with **-----END RSA PRIVATE KEY-----**, for example::

	-----BEGIN RSA PRIVATE KEY-----
	MIICXgIBAAKBgQC0vGnRvfKR0ixKupDk+jxtuRjAc6YkrNaF8Jzy0zlTmGq33xdM
	Uge55OcsW6seQbHiTc9jcck6XiTpPBVxCO6k20uqGAIMh/9hzK+QM96Shi0XSIyU
	K6bt6qSZM0cPNqhj9jJJlZFGHjpfQWRFKZNRps4sK4Ww9M3CSEwNJp1KpwIDAQAB
	AoGAZ3rVD4y03L68M1Eccq2/eYcX3+CXSLpY3TlFc1ZypSVIPNyTh1QULmAb5+7Y
	S6uLgKnSSvq0HyIV+iA3mo9lqrDu2HeGKFcRQJX2hyCAiSrq9eSjl7jzKXZt4pWd
	HNsgk6ydJt15upqcHeG5Qw0rqZpY+BxF14iJxmYMH06jjQECQQDpV9xMrxNjb82D
	Id/cM9dX5jotFIsiL89Ippo3neHYVdGLz48fBo+1xsApM27jcK+7ZZDE2komEfHM
	NUM1eEKBAkEAxkjpYACQvKFD9vruB/gPN430i5YUPFygCZ+Q4p/ZAb7OHijEo06S
	YgwYAOzVAIVHwqktv5uU4k4kt19yI2SpJwJBANomkAkJLOEr50CPbNBbjxnYXc9D
	g4gklm/fghI5AqnUIaHKHI3u/m/9Li3WrfbopQJXw+6l/eh1ok8+BGV61wECQQCf
	gm4DJdFJfW3AVLKBxKLxLQhJ9kyHFnhD5ZJXTQH0rnr/tgoh2YZWy6XPsLXVOmK1
	DQXZex41Q2mz/ltCb6rHAkEAwaFEfGNzVDnRkXGkoME+erT+SdXGcYNW+Lwsq/mH
	73BBkgaxGsRLoA1I8WzjeHrTFX5Yfx90DUr0X+F0OOMEDg==
	-----END RSA PRIVATE KEY-----

Other initial settings
^^^^^^^^^^^^^^^^^^^^^^^^^

Other settings such as API version, request ID generator. You don't configure as default::
	
	// set API version
	client.version = 'v1' 
	// set request ID generator, generator need to return unique String and length is less than 256
	// sdk default generator is uuid.uuid4
	def custom_request_id_generator():
		pass 
	client.request_id_generator = custom_request_id_generator

When client is created, you can use client method to send request message

Create attestation
------------------

::

	// use client create_attestation method to create CreateAttestationResponse instance, if it can be created then return attestation, or throw failure message
	try:
		response = client.create_attestation({
			// set template id
			'template_id': '5Yhus2mVSMnQRXobRJCYgt',
			// set attestation owner's identities
			'identities': {
			    'ID': '42012319800127691X',
			    'MO': '15857112383'
			},
			// factoids list
			'factoids': [
			    {
			        'type': 'product',
			        'data': {
			            'name': 'zjmax',
			            'description': 'p2g financing platform'
			        }
			    },
			    {
			        'type': 'user',
			        'data': {
			            'name': 'Tom Hammond',
			            'phone_number': '13234568732',
			            'registered_at': '1466674609',
			            'username': 'tom'
			        }
			    }
			],
			// set factoids whether upload or not, if set completed as true, then you can't append factoids
			'completed': true
		})
		print(response['data']['no'])
	except ServerException as e:
		print(e.message)

If you want to upload attachments for attestation::

	// create three attachments, which contain one product factoid and two user factoids. The key of list of attachments is the superscript of factoid in factoid list.
	// each attachment contain resource, resource_name and resource_content_type. Resource is a byte array.
	attachments = {
		0: [
			{
			    'resource': open(os.path.dirname(__file__) + '/resources/seal.png', 'rb').read(),
			    'resource_name': 'seal.png',
			    'resource_content_type': 'image/png'
			}
		],
		1: [
			{
			    'resource': open(os.path.dirname(__file__) + '/resources/seal.png', 'rb').read(),
			    'resource_name': 'seal.png',
			    'resource_content_type': 'image/png'
			},
			{
			    'resource': open(os.path.dirname(__file__) + '/resources/contract.pdf', 'rb').read(),
			    'resource_name': 'contract.pdf',
			    'resource_content_type': 'application/pdf'
			}
		]
	}
	// use client create_attestation method to create CreateAttestationResponse  instance, if it's success then return attestation, or return failure
	// ommit to create payload
	try:
		response = client.create_attestation(payload, attachments)
		print(response['data']['no'])
	except ServerException as e:
		print(e.message)

Add factoids
------------------

::

	try:
		response = client.add_factoids({
			// set reference number of attestation
			'ano': '7F189BBB5FA1451EA8601D0693E36FE7',
			// factoids list
			'factoids': [
			    {
			        'type': 'user',
			        'data': {
			            'name': 'Tom Hammond',
			            'phone_number': '13234568732',
			            'registered_at': '1466674609',
			            'username': 'tom'
			        }
			    }
			]
			})
		print(response['data']['success'])
	except ServerException as e:
		print(e.message)	

adding factoids can also upload attachment for factoids, as same as just upload attachment.

Get attestation data
-----------------------

::

	try:
		response = client.get_attestation('DB0C8DB14E3C44C7B9FBBE30EB179241')
		print(response['data'])
	except ServerException as e:
		print(e.message)	

get_attestation have two parameters. The first parameter is reference number of attestation. And the second parameter is an array, which can set to returned fields.

Download the attestation file
--------------------------------

::

	try:
		response = client.download_attestation('DB0C8DB14E3C44C7B9FBBE30EB179241')
		with open(response['file_name'], 'wb') as f:
			f.write(response['file_content'])
	except ServerException as e:
		print(e.message)

response contain two fields, one is file_name, and another is a file content, which is presented by byte format.

Apply for Certification
---------------------------

Apply for personal certification::
	
	try:
		response = client.apply_ca({
			'type': 'PERSONAL',
			'link_name': 'Richard Hammond',
			'link_id_card': '330184198501184115',
			'link_phone': '13378784545',
			'link_email': '123@qq.com',
		})
		print(response['data']['no'])
	except ServerException as e:
		print(e.message)

If enterprise has "three in one" situation, you should use Unified Social Credit Code::

	try:
		response = client.apply_ca({
			'type': 'ENTERPRISE',
			'name': 'xxx Co., Ltd.',
			'ic_code': '91332406MA27XMXJ27',
			'link_name': 'Richard Hammond',
			'link_id_card': '330184198501184115',
			'link_phone': '13378784545',
			'link_email': '123@qq.com',
		})
		print(response['data']['no'])
	except ServerException as e:
		print(e.message)

If not, then use business registration code, organization code, tax code to apply for certification::

	try:
		response = client.apply_ca({
			'type': 'ENTERPRISE',
			'name': 'xxx Co., Ltd.',
			'ic_code': '419001000033792',
			'org_code': '177470403',
			'tax_code': '419001177470403',
			'link_name': 'Richard Hammond',
			'link_id_card': '330184198501184115',
			'link_phone': '13378784545',
			'link_email': '123@qq.com',
		})
		print(response['data']['no'])
	except ServerException as e:
		print(e.message)