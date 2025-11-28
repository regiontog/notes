
```bash
# Parse certificate bundle
openssl storeutl -noout -text -certs /etc/ssl/certs/ca-certificates.crt
```

```bash
# Fetch certificate chain of webserver
openssl s_client -showcerts -servername nrk.no -connect nrk.no:443 </dev/null 2>/dev/null | sed -n -e '/BEGIN\ CERTIFICATE/,/END\ CERTIFICATE/ p'
```

