
```bash
# Trace command file open syscalls 
strace -e trace=open,openat,creat -f -o output.log command_to_trace
```