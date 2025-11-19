```bash
# Krypter hemmelighet
echo -n 'hello' | kubeseal --raw --namespace secret-namespace --name secret-name --cert sealed-secret.crt
```