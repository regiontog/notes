### Get stats / list keyspaces / databases
```bash
redis-cli -h $IP info 
```

### List all keys 
```bash
redis-cli -h $IP keys * 
```
### Get key
```bash
redis-cli -h $IP get $KEY 
```
