---
draft: true
branch: false
title: Deprecation of GlideEncryptor
date: 2024-03-20T22:33:21.304Z
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

