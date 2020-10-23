# awsCrtSign

This program, awsCrtSign, retrieves key material from an AWS KMS RSA Signing Key.
It uses this key material to construct a self-signed X.509v3 certificate.

Why is this useful? Some services like SalesForce use OAuth Bearer tokens to
authenticate to their API via a JSON Web Token (JWT) and require an X.509
certificate to configure their Web App's endpoint. I wanted to use AWS
Lambda to access one of these web applications while keeping my key material
safe. I therefore wanted to put all my key material into a secure key management
environment: AWS KMS. But I still needed the corresponding X.509 certificate.

AWS KMS will sign things for you at $1/month for a key and 3 cents/10,000 API
calls/signatures (at time of writing). Yes, $12/year is a huge and difficult
to justify cost but a Serverless env in my case made this cost efficient.

AWS lets you get the public key material of an asymmetric RSA Key via the AWS
KMS API but not an X509 certificate. You can get AWS to sign a Digest. An X.509
certificate however is essentially only these two things combined with some
metadata. That's where this program comes in.

# awsCrtSign
Retrieves key material from an Amazon KMS RSA Signing Key. It uses this key material to construct a selfsigned X.509 certificate.

### Usage:
```awsCrtSign -arn AWS_key_id -CN "commonName" -ST "stateOrProvince" -O "Organisation" -C "Country" -email emailAddress -years years```


#### AWS_key_id
is the full ARN of the AWS Key you wish to use e.g. `arn:aws:kms:eu-west-1:012345678901:key/12345678-9abc-def0-1234-56789abcdef0`


#### commonName
is the Common Name to be used in the certifiate's subject and issuer field. e.g. `Robot Certificate 1`. It must be an X.500 printableString.


#### stateOrProvince
is the stateOrProvince used in the certifiate's subject and issuer field. e.g. `Manchester`. It must be an X.500 printableString.


#### Organisation
is the Organisation used in the certifiate's subject and issuer field. e.g. `ACME Certificates Inc.` It must be an X.500 printableString.


#### Country
is the Country name to be used in the certifiate's subject and issuer field. e.g. `GB`. It should be an ISO 3166-1 alpha-2 country code. It must be a string of two X.500 printableString characters.


#### emailAddress
is the email address you want in the Issuer Alt Name field. e.g. `info@example.com`. It must be an RFC822 emailAddress. This is optional with no default.


#### years
is the length of time the certificate will be valid for. Default 10. It can be any positive integer as long as "now + *years* years" works with the date command. The expiry date will be encoded as a UTCTIME string i.e. YYMMDDHHMMSSZ so choose wisely!


### The certificate will be self-signed and have a Distinguished name of the form:
`/C=<Country>/ST=<stateOrProvince>/L=<AWS Zone e.g. eu-west-1>/O=<Organisation>/CN=<commonName>`