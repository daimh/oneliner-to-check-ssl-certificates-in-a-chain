# oneliner-to-check-ssl-certificates-in-a-chain
* display all information of all certificats in a chain  
```
openssl s_client -showcerts -connect google.com:443 < /dev/null | sed -ne "/^-----B/,/^-----E/p" | sed -e "s/^-----B/cat << EOF | openssl x509 -noout -text -in - \n-----B/; s/\(^-----E.*\)/\1\nEOF/" | bash
```

* or specifically check 'Signature Algorithm'  
```
openssl s_client -showcerts -connect google.com:443 < /dev/null | sed -ne "/^-----B/,/^-----E/p" | sed -e "s/^-----B/cat << EOF | openssl x509 -noout -text -in - \n-----B/; s/\(^-----E.*\)/\1\nEOF/" | bash | grep 'e A'
```

* Lars Noodén sent me this alternative! Very neat awk usage, thumbs up!
```
openssl s_client \
	-showcerts \
	-connect google.com:443 \
	< /dev/null 2>/dev/null \
| sed -n -e '/^-----B/,/^-----E/p' \
| awk -v a="openssl x509 -noout -text -in -" \
	'$1 {print RS $0|a; close(a)}' RS="-----BEGIN" \
| sed -n -r -e 's/^ {4}(Signature)/\1/p'
```

* Adam Bisaro's cool awk too! thumbs up again!
```
openssl s_client \
	-showcerts \
	-connect google.com:443 \
	< /dev/null 2> /dev/null | \
awk -v x509="openssl x509 -noout -text" \
	'/BEGIN CERT/,/END CERT/ {
		print | x509
		if ( $0 ~ /END/ ){
			close(x509);
			printf("\n");
		}
	}'
```
