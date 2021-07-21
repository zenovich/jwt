# JWT
A [JWT](https://jwt.io/) implementation that uses [nkeys](https://github.com/nats-io/nkeys) to digitally sign JWT tokens.
Nkeys use [secp256k1](https://pkg.go.dev/github.com/ethereum/go-ethereum@v1.10.5/crypto/secp256k1) to provide authentication of JWT claims.


[![License Apache 2](https://img.shields.io/badge/License-Apache2-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0)
[![ReportCard](https://goreportcard.com/badge/github.com/nats-io/jwt)](https://goreportcard.com/report/nats-io/jwt)
[![Build Status](https://travis-ci.com/nats-io/jwt.svg?branch=master)](https://travis-ci.com/github/nats-io/jwt)
[![GoDoc](https://godoc.org/github.com/nats-io/jwt?status.png)](https://godoc.org/github.com/nats-io/jwt)
[![Coverage Status](https://coveralls.io/repos/github/nats-io/jwt/badge.svg?branch=master&t=NmEFup)](https://coveralls.io/github/nats-io/jwt?branch=master)

```go
// Need a private key to sign the claim, nkeys makes it easy to create
kp, err := nkeys.CreateAccount()
if err != nil {
    t.Fatal("unable to create account key", err)
}

pk, err := kp.PublicKey()
if err != nil {
	t.Fatal("error getting public key", err)
}

// create a new claim
claims := NewAccountClaims(pk)
claims.Expires = time.Now().Add(time.Duration(time.Hour)).Unix()


// add details by modifying claims.Account

// serialize the claim to a JWT token
token, err := claims.Encode(kp)
if err != nil {
    t.Fatal("error encoding token", err)
}

// on the receiving side, decode the token
c, err := DecodeAccountClaims(token)
if err != nil {
    t.Fatal(err)
}

// if the token was decoded, it means that it
// validated and it wasn't tampered. the remaining and
// required test is to insure the issuer is trusted
pk, err := kp.PublicKey()
if err != nil {
    t.Fatalf("unable to read public key: %v", err)
}

if c.Issuer != pk {
    t.Fatalf("the public key is not trusted")
}
```