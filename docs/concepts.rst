Overview
===============

For a better experience of using Baoquan.com API, you need to have a basic concept about terminology involved.

Member
---------------

Member is the signed up clients of Baoquan.com, and is also the user of Baoquan.com API. Member need to register on the Baoquan.com, and through API member can send data to Baoquan.com and realise the attestation of data.

Member could be company, organisation or personal individual.

User
---------------

User is the owner of certain attestation. User can inquiry the content of attestation by a reference number. After registered and passed through identity verification process, user is able to inquiry his/her/its attestation by his/her/its phone number and ID number.

User could be company, organisation or personal individual.

Attestation
-------------------

Attestation is a piece of user data record and proof. Every attestation has a unique reference number which matches a specific owner. Through the reference number, user could inquiry the data that has been attested and apply for notarisation.

Every attestation has its linked template by its own, users could define the style of attestation by editing template.

Main data entries of attestation are: Reference number, Identity information, Template ID, Factoid set.

Identity
-------------------

Identity is required when creating the attestation. Identity is the identity information of attestation owner, you can bind the identity information of attestation owner, for example the ID number, Phone number and Uniform credit code together with the attestation, so that users could easily inquiry his/her/its own attestation data. One single user could also have multiple identities.

Identity Code List:

=====  ========================= 
Code   Type  
=====  ========================= 
ID     ID Card 
MO     Mobile phone number
USCID  Uniform credit code
=====  ========================= 

Template
---------------

Template is the data structure user compile on Baoquan.com by the template editor. Template provides an easy way for member to define a piece of legally bind electronic data.

.. image:: images/template.png

Factoid
---------------

Factoid is the data segment Baoquan.com received which is related to some attestation. One attestation could only include one factoid or several factoids. Through API member could send all related factoid of an attestation once for all, or send them separately by multiple API requests.

