# PASETO - a better alternative to JWT

* Stands for Platform Agnostic SEcurity Token
* Here's how a PASETO token looks like:

```
v4.local.OPJGFORYNS3QmwllF3YSHTjtFzZxewVK1Hl1jpfq3ZCdoV5wyro37YJU30S2tkFWLPx709hcaQ4LzXuOdFGJ_6KIWobqcggDW6S5tSViDrcmgv2lp1osn-y-XhzFDa7pepqU0Zevv3ZiJVE0Thx1tjVW8U4gC_1CKVi-7tWqzKtpILRWwp0v-KiTW0JMt14LRyKpyrc.L0NvbnRyb2wvTG9n
```

* It is a period (.) delimited string.
* The 1st block represents the PASETO version.
* The 2nd block refers to the cryptography scheme used. It can be
  `local` (symmetric cryptography) or `public` (asymmetric / public-key
  cryptography).
* The 3rd block is the payload. Based on the cryptography scheme it can
  either be: encrypted and signed (in case of `local`) or only signed
  (in case of `public`).
* The last block is optional is called as the footer. The footer is
  always unencrypted and unsigned regardless of the cryptography scheme.
  It is used to store additional information not related to the actual
  payload.

## Related

* <https://paseto.io>
* <https://developer.okta.com/blog/2019/10/17/a-thorough-introduction-to-paseto>

```go
package main

import (
	"fmt"
	"log"
	"time"

	"aidanwoods.dev/go-paseto"
)

const (
	footer   = "/Control/Log"
	implicit = "implicit bytes"
)

func main() {
	t := paseto.NewToken()
	t.SetFooter([]byte(footer))
	t.SetString("role", "daemon")
	t.SetIssuedAt(time.Now())
	t.SetExpiration(time.Now().Add(time.Minute * 5))

	// must be kept secret
	key := paseto.NewV4SymmetricKey()
	fmt.Printf("Secret key: %s\n", key.ExportHex())

	enc := t.V4Encrypt(key, []byte(implicit))
	fmt.Printf("Token: %s\n", enc)

	// extract footer without decrypting the token
	p := paseto.NewParser()
	f, err := p.UnsafeParseFooter(paseto.V4Local, enc)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("footer: %s\n", string(f))

	// decrypt the token
	token, err := p.ParseV4Local(key, enc, []byte(implicit))
	if err != nil {
		log.Fatal(err)
	}

	role, err := token.GetString("role")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("role: %s\n", role)
}
```


  #cryptography #paseto
