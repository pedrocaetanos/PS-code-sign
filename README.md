# PS-code-sign
Create Code Signing Certificate on Windows for PS scripts

## Windows 7
__New-SelfSignedCertificateEx__ Self-signed certificate generator (PowerShell script)
https://gallery.technet.microsoft.com/scriptcenter/self-signed-certificate-5920a7c6
```
New-SelfsignedCertificateEx -Subject "CN=Test Code Signing" `
-EKU "Code Signing" `
-KeySpec "Signature" ` 
-KeyUsage "DigitalSignature" `
-FriendlyName "Pedro Caetano S."
-NotAfter $([datetime]::now.AddYears(5))
```

## Windows 8
__certreq.exe__ built-in
```
Certreq –new path\inffilename.inf
```

inffilename.inf:
```
[NewRequest]
Subject = "CN=Pedro Caetano S."
KeyLength = 2048
KeyAlgorithm = RSA
ProviderName = "Microsoft Enhanced RSA and AES Cryptographic Provider"
MachineKeySet = false
Exportable = true
KeySpec = 2
KeyUsage = 0x80
RequestType = Cert
[EnhancedKeyUsageExtension]
OID=1.3.6.1.5.5.7.3.3 ; Code signing
```

## Windows 10
__New-SelfSignedCertificate__ PowerShell cmdlet
```
New-SelfSignedCertificate -CertStoreLocation cert:\currentuser\my `
-Subject "CN=Pedro Caetano S." `
-HashAlgorithm SHA256 `
-Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
-KeyExportPolicy Exportable `
-KeyUsage DigitalSignature `
-Type CodeSigningCert
```

## Alternative
__CreateCertGUI__

## Util
Dir code signing certificates
```
gci cert: -rec -code
```

Sign script
```
Set-AuthenticodeSignature script.ps1 @(Get-ChildItem -Path cert:\CurrentUser\My -CodeSigningCert)
```

## Comments
Import the certificate into ___Trusted Root Certification Authorities___ to be able to run a signed script with the AllSigned or
RemoteSigned execution policy.

Answering ___[A] Always run___ to the first time prompt will import the certificate into ___Trusted Publishers___.

However, self-signed certificate usage for code signing in production environments is discouraged. You should use them in test environments only.

For private usage (within organization only), you should check if company already owns PKI infrastructure and contact appropriate personnel to receive company-approved code signing certificate.

For public scripts (you are going to distribute along with software packages, or deliver scripts to your customers), I would suggest to purchase code signing from globally trusted commercial CA provider.

## Reference
https://serverfault.com/questions/824574/create-code-signing-certificate-on-windows-for-signing-powershell-scripts  
https://ss64.com/ps/set-authenticodesignature.html  
https://www.hanselman.com/blog/SigningPowerShellScripts.aspx  

## Additional documentation
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_signing?view=powershell-7.2
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-authenticodesignature?view=powershell-7.2
https://docs.microsoft.com/en-us/previous-versions/windows/internet-explorer/ie-developer/platform-apis/ms537361(v=vs.85)
