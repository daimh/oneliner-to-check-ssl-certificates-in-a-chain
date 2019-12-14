# oneliner-to-check-ssl-certificates-in-a-chain

```
openssl s_client -showcerts -connect google.com:443 < /dev/null | sed -ne "/^-----B/,/^-----E/p" | sed -e "s/^-----B/cat << EOF | openssl x509 -noout -text -in - \n-----B/; s/\(^-----E.*\)/\1\nEOF/" | bash | grep 'e A'
```
