Java
=================

If you use maven, you can add dependency as below::

	<dependency>
	    <groupId>com.baoquan</groupId>
	    <artifactId>eagle-sdk</artifactId>
	    <version>1.0.8</version>
	</dependency>

If you use Gradle, you can add like following::
	
	compile group: 'com.baoquan', name: 'eagle-sdk', version: '1.0.8'

Create Baoquan Client
---------------------------

::

	BaoquanClient client = new BaoquanClient();
	// set API address, like Baoquan test environment
	client.setHost("https://baoquan.com"); 
	// set access key
	client.setAccessKey("fsBswNzfECKZH9aWyh47fc"); 
	// set absolute path of rsa private key file
	client.setPemPath("path/to/rsa_private.pem"); 

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

Other initial settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Other settings such as API version, request ID generator. You don't configure as default::
	
	// set API version
	client.setVersion("v1") 
	// set request ID generator, generator need to implement createRequestId method in interface RequestIdGenerator
	client.setRequestIdGenerator(CustomRequestGenerator) 

	// Default SDK request ID generator
	public class DefaultRequestIdGenerator implements RequestIdGenerator {

		@Override
		public String createRequestId() {
			return UUID.randomUUID().toString();
		}
	}

When client is created, you can use client method to send request message

Create attestation
---------------------

::

	CreateAttestationPayload payload = new CreateAttestationPayload();
	// set unique id
 	payload.setUniqueId("e68eb8bc-3d7a-4e22-be47-d7999fb40c9a");
	// set template id
	payload.setTemplateId("5Yhus2mVSMnQRXobRJCYgt"); 
	// set factoids whether upload or not, if set completed as true, then you can't append factoids
	payload.setCompleted(false); 
	// set the owner identities, the type is defined at IdentityType
	Map<IdentityType, String> identities = new HashMap<>();
	identities.put(IdentityType.ID, "42012319800127691X");
	identities.put(IdentityType.MO, "15857112383");
	payload.setIdentities(identities);
	// add factoid list
	List<Factoid> factoids = new ArrayList<>();
	// add product factoid
	Factoid factoid = new Factoid();
	Product product = new Product();
	product.setName("xxx company");
	product.setDescription("p2g financing platform");
	factoid.setUnique_id("e13912e2-ccce-47df-997a-9f44eb2c7b6c");
	factoid.setType("product");
	factoid.setData(product);
	factoids.add(factoid);
	// add user factoid
	factoid = new Factoid();
	User user = new User();
	user.setName("Tom Hammond");
	user.setRegistered_at("1466674609");
	user.setUsername("tom");
	user.setPhone_number("13452345987");
	factoid.setUnique_id("5bf54bc4-ec69-4a5d-b6e4-a3f670f795f3");
	factoid.setType("user");
	factoid.setData(user);
	factoids.add(factoid);
	payload.setFactoids(factoids);
	// use client createAttestation method to create CreateAttestationResponse instance, if it can be created then return attestation, or throw failure message
	try {
		CreateAttestationResponse response = client.createAttestation(payload);
		System.out.println(response.getData().getNo());
	} catch (ServerException e) {
		System.out.println(e.getMessage());
	}

If you want to upload attachments for attestation::

	// create three attachments, each attachment is an instance of ByteArrayBody, and ContentType must set as DEFALUT_BINARY, also you must set filename.
	InputStream inputStream0 = getClass().getClassLoader().getResourceAsStream("seal.png");
	ByteArrayBody byteArrayBody0 = new ByteArrayBody(IOUtils.toByteArray(inputStream0), ContentType.DEFAULT_BINARY, "seal.png");
	InputStream inputStream1 = getClass().getClassLoader().getResourceAsStream("seal.png");
	ByteArrayBody byteArrayBody1 = new ByteArrayBody(IOUtils.toByteArray(inputStream1), ContentType.DEFAULT_BINARY, "seal.png");
	InputStream inputStream2 = getClass().getClassLoader().getResourceAsStream("contract.pdf");
	ByteArrayBody byteArrayBody2 = new ByteArrayBody(IOUtils.toByteArray(inputStream2), ContentType.DEFAULT_BINARY, "contract.pdf");
	// create the map of attachment, which the key is factoids' superscript. For example, we set the first factoid as one attachment, and set the second factoid as two attachment
	Map<String, List<ByteArrayBody>> attachments = new HashMap<>();
	attachments.put("0", Collections.singletonList(byteArrayBody0));
	attachments.put("1", Arrays.asList(byteArrayBody1, byteArrayBody2));
	// ommit to create payload
	try {
		CreateAttestationResponse response = client.createAttestation(payload, attachments);
		System.out.println(response.getData().getNo());
	} catch (ServerException e) {
		System.out.println(e.getMessage());
	}

Add factoids
------------------

::

	AddFactoidsPayload addFactoidsPayload = new AddFactoidsPayload();
	// set attestation reference number
	addFactoidsPayload.setAno("7F189BBB5FA1451EA8601D0693E36FE7");
	// add factoid
	factoids = new ArrayList<>();
	factoid = new Factoid();
	User user = new User();
	user.setName("Tom Hammond");
	user.setRegistered_at("1466674609");
	user.setUsername("tom");
	user.setPhone_number("13452345987");
	factoid.setUnique_id("5bf54bc4-ec69-4a5d-b6e4-a3f670f795f3");
	factoid.setType("user");
	factoid.setData(user);
	factoids.add(factoid);
	addFactoidsPayload.setFactoids(factoids);
	// use client addFactoids method to create AddFactoidsResponse instance, if it's successn then return attestation, or return failure
	try {
		AddFactoidsResponse response = client.addFactoids(addFactoidsPayload);
		System.out.println(response.getData().isSuccess());
	} catch (ServerException e) {
		System.out.println(e.getMessage());
	}

adding factoids can also upload attachment for factoids, as same as just upload attachment.

Get attestation data 
--------------------------

::

	try {
		GetAttestationResponse response = client.getAttestation("DB0C8DB14E3C44C7B9FBBE30EB179241", null);
		System.out.println(response.getData());
	} catch (ServerException e) {
		System.out.println(e.getMessage());
	}	

getAttestation have two parameters. The first parameter is reference number of attestation. And the second parameter is an array, which can set to returned fields.

Download the attestation file
------------------------------------

::

	try {
		DownloadFile downloadFile = client.downloadAttestation("7FF4E8F6A6764CD0895146581B2B28AA");

		FileOutputStream fileOutputStream = new FileOutputStream(downloadFile.getFileName());
		IOUtils.copy(downloadFile.getFile(), fileOutputStream);
		fileOutputStream.close();
	} catch (ServerException e) {
		System.out.println(e.getMessage());
	}

Apply for Certification
---------------------------

Apply for personal certification::
	
	try {
		ApplyCaPayload payload = new ApplyCaPayload();
		payload.setType(CaType.PERSONAL);
		payload.setLinkName("Richard Hammond");
		payload.setLinkIdCard("330184198501184115");
		payload.setLinkPhone("13378784545");
		payload.setLinkEmail("123@qq.com");
		ApplyCaResponse response = client.applyCa(payload, null);
		System.out.println(response.getData().getNo());
	} catch (ServerException e) {
		System.out.println(e.getMessage());
	}

If enterprise has "three in one" situation, you should use Unified Social Credit Code::

	try {
		ApplyCaPayload payload = new ApplyCaPayload();
		payload.setType(CaType.ENTERPRISE);
		payload.setName("xxx Co., Ltd.");
		payload.setIcCode("91332406MA27XMXJ27");
		payload.setLinkName("Richard Hammond");
		payload.setLinkIdCard("330184198501184115");
		payload.setLinkPhone("13378784545");
		payload.setLinkEmail("123@qq.com");
		ApplyCaResponse response = client.applyCa(payload, null);
		System.out.println(response.getData().getNo());
	} catch (ServerException e) {
		System.out.println(e.getMessage());
	}

If not, then use business registration code, organization code, tax code to apply for certification::

	try {
		ApplyCaPayload payload = new ApplyCaPayload();
		payload.setType(CaType.ENTERPRISE);
		payload.setName("xxx Co., Ltd.");
		payload.setIcCode("419001000033792");
		payload.setOrgCode("177470403");
		payload.setTaxCode("419001177470403");
		payload.setLinkName("Richard Hammond");
		payload.setLinkIdCard("330184198501184115");
		payload.setLinkPhone("13378784545");
		payload.setLinkEmail("123@qq.com");
		ApplyCaResponse response = client.applyCa(payload, null);
		System.out.println(response.getData().getNo());
	} catch (ServerException e) {
		System.out.println(e.getMessage());
	}