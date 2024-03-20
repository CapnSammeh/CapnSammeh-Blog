---
draft: true
branch: false
title: Deprecation of GlideEncryptor
date: 2024-03-20T22:33:21.304Z
thumbnail: >-
  https://res.cloudinary.com/capnsammeh/image/upload/c_scale,f_auto,q_auto,w_400/v1710974148/photo-1709572374695-e0a94e1ec340_mttofp.jpg
showComments: true
---
For years, we've used an API called ***GlideEncrypter*** to provide methods for encrypting and decrypting content within ServiceNow using the Triple DES Algorithm. While this has been sufficient for some, it problematically doesn't align with [NIST 800-131A R2](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-131Ar2.pdf)'s recommendation (published in March of 2019) to no longer use Triple DES for encryption of data post-2023. 

Given it's March of 2024, it's critical that we understand the alternatives to using GlideEncryptor across the ServiceNow platform, and propose a means to find any wayward usages of this function and it's methods across our technology sprawl. 

---

***Note: some of this content can also be found in the following linked KBA provided by ServiceNow: [LINK.](https://support.servicenow.com/kb?id=kb_article_view&sysparm_article=KB1320986)***

---

### What to expect

ServiceNow offers alternative cryptographic solutions to the GlideEncrypter API

* GlideElement API to encrypt/decrypt password2 values through GlideRecord
* Key Management Framework that provides a secure and comprehensive interface for cryptographic key management services on the instance

If you opt to use GlideEncrypter API after December 31, 2023, then please be aware that

* New instance installations and re-installations (zboots) using the GlideEncrypter API in the Now Platform® will not be permitted in the Xanadu release, planned for Sept 2024.

  * Note: this deadline has been moved from Washington DC release in March 2024 to September 2024.
* Existing instances using the GlideEncrypter API in the Now Platform® will not be permitted in the Xanadu release, planned for Sept 2024.

---

### Understand the impact to your instance 

To identify where GlideEncrypter is used on your instance, run the scan check tool. Find this tool on your instance by navigating to Scan > Suites > Deprecated APIs > Deprecated API: GlideEncrypter. Find more information on Instance Scan here. 

The scan results show each use of GlideEncrypter, with the source and a proposed resolution using alternative encryption methods listed below. 

---

### Deprecation Steps 

Run the scan check Scan > Suites > Deprecated APIs > Deprecated API: GlideEncrypter to identify the records with GlideEncrypter usages.
Remediate the GlideEncrypter usages with code changes for the chosen alternative. Be sure to update the related tests as well if any.
Test to make sure there are no errors resulting from the changes. 

---
### Platform Alternatives 

Platform encryption APIs and products to consider as alternatives are: 

- GlideElement APIs that automatically encrypts/decrypts password2 values
  - [GlideElement - getDecryptedValue()](https://docs.servicenow.com/bundle/utah-api-reference/page/app-store/dev_portal/API_reference/glideElement/concept/c_GlideElementScopedAPI.html#title_SGE-getDecryptedValue) to decrypt password2 value
  - [GlideElement - setDisplayValue(Object value)](https://docs.servicenow.com/bundle/utah-api-reference/page/app-store/dev_portal/API_reference/glideElement/concept/c_GlideElementScopedAPI.html#title_r_ScopedGlideElementSetDisplayValue_Object_value) to encrypt password2 value
- [Key Management Framework (KMF)](https://docs.servicenow.com/bundle/tokyo-platform-security/page/administer/key-management-framework/concept/understanding-kmf.html)
---

### GlideEncrypter Deprecation Use Cases

| Custom usage to decrypt password2 values |
|---|---|
|testing||testing2|

Example usage 

var decryptedPwd = new GlideEncrypter().decrypt(gr.password);

Alternatives 

You can use getDecryptedValue() API of the Password2 field

Sample code:

var decryptedPwd = gr.password2field.getDecryptedValue();
Note: getDecryptedValue’s cross-scope access policy doesn’t allow cross-scope calls. If a record is in scope A, decryption can only be done on scope A. If a record is in scope B, decryption can only be done on scope B. A value in scope A won’t be allowed to be decrypted on scope B. 

Release Version 

Vancouver Patch 1  

 

Custom usage to encrypt password2 

Example usage 

//encrypt password value
var encryptedPassword = new GlideEncrypter().encrypt(clearTextValue);
//set the encrypted value to password field 
gr.setValue("password", encryptedPassword );
//insert or update 
gr.insert();
Alternatives 

Use GlideElement.setDisplayValue(…)

gr.password.setDisplayValue(clearTextValue);
gr.insert();
 

Release Version 

Vancouver Patch 1  

 

Custom usage to encrypt/decrypt non password2 data 

Example usage 

//encrypt value 
var encryptedValue = new GlideEncrypter().encrypt(value);
//set the encrypted value to non-password2 field 
gr.setValue("column", encryptedValue);
Alternatives 

Use KMFCryptoOperation API to perform symmetric data encryption / decryption  

//encrypt
var encryptOp = new sn_kmf_ns.KMFCryptoOperation("< module_name >", "SYMMETRIC_ENCRYPTION")
                .withInputFormat("KMFNone");
var encryptedText = encryptOp.doOperation(clear_text);

//decrypt 
var encryptOp = new sn_kmf_ns.KMFCryptoOperation("< module_name>", "SYMMETRIC_DECRYPTION")
                .withOutputFormat("KMFNone");
var clear_text = encryptOp.doOperation(<encrypted_text>);
To support decryption of both KMF encrypted values and GlideEncrypter encrypted values, use KMF_GLIDE_ENCRYPTER_FORMATTED input format decorator.  

var encryptOp = new sn_kmf_ns.KMFCryptoOperation("<module_name>", "SYMMETRIC_DECRYPTION")
                .withInputFormat("KMF_GLIDE_ENCRYPTER_FORMATTED")
                .withOutputFormat("KMFNone");
var clear_text = encryptOp.doOperation(<encrypted_text>);
 

Note: These operations require a KMF module that supports SYMMETRIC_ENCRYPTION , SYMMETRIC_DECRYPTION and also tracks/allows the Decryptions. If you do not have a module that matches this criteria, you can create a new KMF module and use that in the code as an input parameter for module name.
If you also have a use case to transfer this data to a different instance and expect it to be decrypted there, then you also need to maintain the module and keys in both the source and target instances. You can do this by using KMF key exchange

Release Version 

Vancouver Patch 1  

 

Custom usage to encrypt/decrypt parameters or payload transferred  

Example usage 

Transferring encrypted data between instances via XML export/import, update sets and webservice calls. 

Alternatives 

Use KMFCryptoOperation to perform symmetric data encryption/decryption with the key resource exchanged to target instances (KMF key exchange) 

To support decryption of both KMF encrypted values and glide encrypted values, use KMF_GLIDE_ENCRYPTER_FORMATTED input format decorator. 

Release Version 

Vancouver Patch 1  

 

Custom usage to encrypt/decrypt parameters or payload transferred using custom keys 

Example usage 

var encryptedValue = new GlideEncrypter("<custome_key>").encrypt(value);

Alternatives 

ServiceNow does not recommend using hardcoded custom keys because they may present security risks. Instead, use KMF for your key management and encryption needs.

Let KMF manage keys for the whole lifecycle 

Use a securely managed customer supplied key (configure and upload your customer supplied key) 

Release Version 

Tokyo 

