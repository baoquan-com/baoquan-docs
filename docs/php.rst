PHP
=================

If you use composer, you can add dependency as below::

	{
	  "require": {
	    "baoquan/eagle-sdk": "1.0.7"
	  }
	}

Create Baoquan Client
------------------------

::

	$client = new BaoquanClient();
	// set API address, like Baoquan test environment
	$client->setHost('https://baoquan.com'); 
	// set access key
	$client->setAccessKey('fsBswNzfECKZH9aWyh47fc'); 
	// set absolute path of rsa private key file
	$client->setPemPath('path/to/rsa_private.pem'); 

rsa private key should begin with **-----BEGIN PRIVATE KEY-----** and end with **-----END PRIVATE KEY-----**, for example::

	-----BEGIN PRIVATE KEY-----
	MIICeAIBADANBgkqhkiG9w0BAQEFAASCAmIwggJeAgEAAoGBALS8adG98pHSLEq6
	kOT6PG25GMBzpiSs1oXwnPLTOVOYarffF0xSB7nk5yxbqx5BseJNz2NxyTpeJOk8
	FXEI7qTbS6oYAgyH/2HMr5Az3pKGLRdIjJQrpu3qpJkzRw82qGP2MkmVkUYeOl9B
	ZEUpk1GmziwrhbD0zcJITA0mnUqnAgMBAAECgYBnetUPjLTcvrwzURxyrb95hxff
	4JdIuljdOUVzVnKlJUg83JOHVBQuYBvn7thLq4uAqdJK+rQfIhX6IDeaj2WqsO7Y
	d4YoVxFAlfaHIICJKur15KOXuPMpdm3ilZ0c2yCTrJ0m3Xm6mpwd4blDDSupmlj4
	HEXXiInGZgwfTqONAQJBAOlX3EyvE2NvzYMh39wz11fmOi0UiyIvz0immjed4dhV
	0YvPjx8Gj7XGwCkzbuNwr7tlkMTaSiYR8cw1QzV4QoECQQDGSOlgAJC8oUP2+u4H
	+A83jfSLlhQ8XKAJn5Din9kBvs4eKMSjTpJiDBgA7NUAhUfCqS2/m5TiTiS3X3Ij
	ZKknAkEA2iaQCQks4SvnQI9s0FuPGdhdz0ODiCSWb9+CEjkCqdQhococje7+b/0u
	Ldat9uilAlfD7qX96HWiTz4EZXrXAQJBAJ+CbgMl0Ul9bcBUsoHEovEtCEn2TIcW
	eEPlkldNAfSuev+2CiHZhlbLpc+wtdU6YrUNBdl7HjVDabP+W0JvqscCQQDBoUR8
	Y3NUOdGRcaSgwT56tP5J1cZxg1b4vCyr+YfvcEGSBrEaxEugDUjxbON4etMVflh/
	H3QNSvRf4XQ44wQO
	-----END PRIVATE KEY-----

or begin with **-----BEGIN RSA PRIVATE KEY-----** and end with **-----END RSA PRIVATE KEY-----**, for example::

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
^^^^^^^^^^^^^^^^^^^^^^^^^^

Other settings such as API version, request ID generator. You don't configure as default::
	
	// set API version
	$client->setVersion('v1');
	// set request ID generator, generator need to implement createRequestId method in interface RequestIdGenerator
	$client->setRequestIdGenerator(CustomRequestGenerator);

	// Default SDK request ID generator
	class DefaultRequestIdGenerator implements RequestIdGenerator
	{
	    /**
	     * create request id
	     * @return string
	     */
	    function createRequestId()
	    {
	        return md5(uniqid("", true));
	    }
	}

When client is created, you can use client method to send request message

Create attestation
------------------

::

	// use client createAttestation method to create CreateAttestationResponse instance, if it can be created then return attestation, or throw failure message
	try {
		$response = $client->createAttestation([
			// set template id
			'template_id'=>'5Yhus2mVSMnQRXobRJCYgt', 
			// set attestation owner's identities
			'identities'=>[
			    'ID'=>'42012319800127691X',
			    'MO'=>'15857112383',
			],
			// factoids list
			'factoids'=>[
			    // product factoid
			    [
			        'type'=>'product',
			        'data'=>[
			            'name'=>'zjmax',
			            'description'=>'p2g financing platform'
			        ]
			    ],
			    // user factoid
			    [
			        'type'=>'user',
			        'data'=>[
			            'name'=>'Tom Hammond',
			            'phone_number'=>'13234568732',
			            'registered_at'=>'1466674609',
			            'username'=>'tom'
			        ]
			    ]
			],
			// set factoids whether upload or not, if set completed as true, then you can't append factoids
			'completed'=>true
			]
		);
		echo $response['data']['no'];
	} catch (ServerException $e) {
		echo $e->getMessage();
	}

If you want to upload attachments for attestation::

	// create three attachments, which contain one product factoid and two user factoids. The key of list of attachments is the superscript of factoid in factoid list.
	// each attachment contain resource and resource_name, and resource is an instance of php.
	$attachments = [
		0=>[
		    [
		        'resource'=>fopen(__DIR__.'/resources/seal.png', 'r'),
		        'resource_name'=>'seal.png'
		    ]
		],
		1=>[
		    [
		        'resource'=>fopen(__DIR__.'/resources/seal.png', 'r'),
		        'resource_name'=>'seal.png'
		    ],
		    [
		        'resource'=>fopen(__DIR__.'/resources/contract.pdf', 'r'),
		        'resource_name'=>'contract.pdf'
		    ]
		]
	];

	// use client createAttestation method to create CreateAttestationResponse  instance, if it's success then return attestation, or return failure message
	// ommit to create payload
	try {
		$response = $client->createAttestation($payload, $attachments);
		echo $response['data']['no'];
	} catch (ServerException $e) {
		echo $e->getMessage();
	}

Add factoids
------------------

::

	try {
		$response = $client->addFactoids([
			// set attestation reference number
			'ano'=>'7F189BBB5FA1451EA8601D0693E36FE7', 
			// list of factoids
			'factoids'=>[
			    [
			        'type'=>'user',
			        'data'=>[
			            'name'=>'Tom Hammond',
			            'phone_number'=>'13234568732',
			            'registered_at'=>'1466674609',
			            'username'=>'tom'
			        ]
			    ]
			],
			'completed'=>true
			]
		);
		echo $response['data']['success'];
	} catch (ServerException $e) {
		echo $e->getMessage();
	}

adding factoids can also upload attachment for factoids, as same as just upload attachment.

Get attestation data
----------------------------

::

	try {
		$response = $client->getAttestation('DB0C8DB14E3C44C7B9FBBE30EB179241');
		var_dump($response['data']);
	} catch (ServerException $e) {
		echo $e->getMessage();
	}

getAttestation have two parameters. The first parameter is reference number of attestation. And the second parameter is an array, which can set to returned fields.

Download the attestation file
--------------------------------

::

	try {
		$response = $client->downloadAttestation('DB0C8DB14E3C44C7B9FBBE30EB179241');
		$file = fopen($response['file_name'], 'w');
		fwrite($file, $response['file']->getContents());
		fclose($file);
	} catch (ServerException $e) {
		echo $e->getMessage();
	}

response contain two fields, one is file_name, and another is file, which is an instance of \\Psr\\Http\\Message\\StreamInterface.

Apply for Certification
---------------------------

Apply for personal certification::
	
	try {
		$response = $client->applyCa([
			'type'=>'PERSONAL',
			'link_name'=>'Richard Hammond',
			'link_id_card'=>'330184198501184115',
			'link_phone'=>'13378784545',
			'link_email'=>'123@qq.com',
		]);
		echo $response['data']['no'];
	} catch (ServerException $e) {
		echo $e->getMessage();
	}

If enterprise has "three in one" situation, you should use Unified Social Credit Code::

	try {
		$response = $client->applyCa([
			'type'=>'ENTERPRISE',
			'name'=>'xxx Co., Ltd.',
			'ic_code'=>'91332406MA27XMXJ27',
			'link_name'=>'Richard Hammond',
			'link_id_card'=>'330184198501184115',
			'link_phone'=>'13378784545',
			'link_email'=>'123@qq.com',
		]);
		echo $response['data']['no'];
	} catch (ServerException $e) {
		echo $e->getMessage();
	}

If not, then use business registration code, organization code, tax code to apply for certification::

	try {
		$response = $client->applyCa([
			'type'=>'ENTERPRISE',
			'name'=>'xxx Co., Ltd.',
			'ic_code'=>'419001000033792',
			'org_code'=>'177470403',
			'tax_code'=>'419001177470403',
			'link_name'=>'Richard Hammond',
			'link_id_card'=>'330184198501184115',
			'link_phone'=>'13378784545',
			'link_email'=>'123@qq.com',
		]);
		echo $response['data']['no'];
	} catch (ServerException $e) {
		echo $e->getMessage();
	}
