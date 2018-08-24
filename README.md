# Keystore Tools

## Introduction

This toolkit includes a suite of handy wrappers of `keytool`
for manipulating PKCS-12 key stores.

The following is an example showing the procedure of
creating a CA and signing certificates with it.

### Creating a CA

The first thing to do is to create a CA, let's call the CA "Boss".
Obviously, the CA needs a public/private key pair,
as well as a certificate containing its public key,
so that others can import the certificate and trust the CA.

First we create the key pair with:

```gen_ca_key_pair Boss 365```

After setting the password and other information,
we will get a file called `Boss.p12`,
which contains the public/private key pair of Boss valid for 365 days.

Then we get the certificate of Boss with

```export_certificate Boss.p12```

After typing the password of Boss,
we will get a file called `Boss.pem`.
This is the self-signed certificate of Boss,
we can share this certificate with others,
so that they can trust Boss CA as well as the certificates signed by it.

### Request a signed certificate

Consider a department called Alpha
who wants to get a certificate signed by Boss.

First, we need to create a public/private key pair with:

```gen_key_pair Alpha 365```

After setting the password and other information,
we will get a file called `Alpha.p12`,
which contains the public/private key pair of Alpha valid for 365 days.

Then we need to trust Boss (of course),
so that we can request a signature from Boss.
So we ask Boss for its certificate `Boss.pem`,
then we trust this certificate with:

```import_certificate Boss.pem Alpha.p12```

Now we can ask Boss to sign our certificate.
We generate a Certificate Signature Request (CSR) with:

```gen_csr Alpha.p12```

Then we will get a file `Alpha.csr`.
We can send this file to Boss to request a signed certificate.

### Sign a certificate

Now switch to Boss.
We just received a CSR `Alpha.csr` from Alpha Department,
and we agree to sign it.
So we can sign it with:

```sign_certificate Alpha.csr Boss.p12 365```

We will get a file `Alpha.pem`,
which is the certificate of Alpha
signed by Boss valid for 365 days.
We can then send this signed certificate back to Alpha Department.

### Import the signed certificate

Now switch back to Alpha.
We just received the signed certificate `Alpha.pem` from Boss,
now we just need to update our key pair
with this signed certificate with:

```import_certificate Alpha.pem Alpha.p12```
