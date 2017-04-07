

## Convert pfx certificate to base64 string using Powershell

Use the below script to convert pfx certificate to Base64 string using Powershell. This is needed for ADLS access.

```

$fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

[System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

```
