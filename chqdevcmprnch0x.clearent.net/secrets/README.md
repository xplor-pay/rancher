### Secrets must be created in each namespace
```
kubectl.exe -n class-a-plus create secret generic keys \
  --from-file=krb5.conf=krb5.conf \
  --from-file=signing-dev.clearentsso.net.crt=signing-dev.clearentsso.net.crt \
  --from-file=signing.pf.clearentsso.com.cer=signing.pf.clearentsso.com.cer \
  --from-file=sqlauth.keytab=sqlauth.keytab
```

```
kubectl.exe -n class-a create secret generic keys \
  --from-file=krb5.conf=krb5.conf \
  --from-file=signing-dev.clearentsso.net.crt=signing-dev.clearentsso.net.crt \
  --from-file=signing.pf.clearentsso.com.cer=signing.pf.clearentsso.com.cer \
  --from-file=sqlauth.keytab=sqlauth.keytab
```

```
kubectl.exe -n class-b create secret generic keys \
  --from-file=krb5.conf=krb5.conf \
  --from-file=signing-dev.clearentsso.net.crt=signing-dev.clearentsso.net.crt \
  --from-file=signing.pf.clearentsso.com.cer=signing.pf.clearentsso.com.cer \
  --from-file=sqlauth.keytab=sqlauth.keytab
```

```
kubectl.exe -n class-c create secret generic keys \
  --from-file=krb5.conf=krb5.conf \
  --from-file=signing-dev.clearentsso.net.crt=signing-dev.clearentsso.net.crt \
  --from-file=signing.pf.clearentsso.com.cer=signing.pf.clearentsso.com.cer \
  --from-file=sqlauth.keytab=sqlauth.keytab
```