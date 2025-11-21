```bash
# Encrypt secret
echo -n 'hello' | kubeseal --raw --namespace secret-namespace --name secret-name --cert sealed-secret.crt
```
```bash
# Encrypt secret without the sealedsecret operator validating the secret name(only validates the namespace)
echo -n 'hello' | kubeseal --raw --namespace secret-namespace --scope namespace-wide --cert sealed-secret.crt
```