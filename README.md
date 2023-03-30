# Manuals

## PING сразу нескольких машин по очереди.

```bash
for i in {18..19}; do ping -c3 "192.168.1.$i"; done
```
