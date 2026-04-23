# H2A POS Deployment Guide

## Overview
This guide explains how to deploy the H2A POS application to your client with automatic update capabilities while avoiding Windows security blocking.

## What You'll Need
1. **Web server or file hosting** for update files (can be GitHub Pages, AWS S3, or any static file host)
2. **PowerShell** (included with Windows) for build scripts
3. **Administrator access** on client machines for initial installation

## Step 1: Build and Package

1. Open PowerShell as Administrator
2. Navigate to the `deploy` folder
3. Run the build script:
```powershell
.\build-and-deploy.ps1 -Version "1.0.0" -UpdateServerUrl "https://your-server.com/h2a-pos"
```

This creates:
- `H2A_POS.exe` - Self-contained application
- `install.bat` - Installation script
- `update.json` - Update configuration
- `H2A_POS_v1.0.0.zip` - Distribution package

## Step 2: Set Up Update Server

1. Upload `H2A_POS.exe` to your web server
2. Upload `update.json` to the same location
3. Ensure both files are publicly accessible via HTTP/HTTPS

Example URLs:
- `https://your-server.com/h2a-pos/H2A_POS.exe`
- `https://your-server.com/h2a-pos/update.json`

## Step 3: Update Application Configuration

Edit `Services/UpdateService.cs` and replace the placeholder URL:
```csharp
private readonly string _updateUrl = "https://your-server.com/h2a-pos/update.json";
```

Rebuild the application after this change.

## Step 4: Client Installation

1. Extract `H2A_POS_v1.0.0.zip` on client machine
2. Right-click `install.bat` and "Run as administrator"
3. Follow the installation prompts

The installer will:
- Copy files to `C:\Program Files\H2A_POS\`
- Create desktop and Start Menu shortcuts
- Add firewall exception
- Set up proper file permissions

## Step 5: Update Process

### Automatic Updates
- Application checks for updates on startup
- Users see a prompt when updates are available
- Updates download and install automatically
- Application restarts with new version

### Manual Updates
1. Download latest `H2A_POS.exe` from your server
2. Replace the file in `C:\Program Files\H2A_POS\`
3. Launch application

## Avoiding Windows Security Issues

### What We've Done:
1. **Self-contained executable** - No .NET runtime installation required
2. **Administrator installation** - Properly registers with Windows
3. **Firewall exception** - Prevents network blocking
4. **Signed batch files** - Uses Windows native scripts
5. **No registry hacks** - Clean, standard installation

### Additional Recommendations:
1. **Use HTTPS** for your update server to prevent download warnings
2. **Test on clean Windows 10/11 machines** before client deployment
3. **Consider Windows Defender Application Control** exclusion if needed
4. **Document the process** for IT administrators

## Version Management

When releasing updates:
1. Increment version number: `.\build-and-deploy.ps1 -Version "1.0.1"`
2. Upload new `H2A_POS.exe` and `update.json` to server
3. Clients will automatically detect and install updates

## Troubleshooting

### "Windows protected your PC"
- Right-click → Properties → Unblock
- Or run as administrator

### "Cannot connect to update server"
- Check firewall settings
- Verify URL accessibility in browser
- Ensure HTTPS certificate is valid

### Installation fails
- Run as administrator
- Check antivirus software
- Verify sufficient disk space

## Hosting Options

### Free Options:
- **GitHub Pages** - Free static hosting
- **Netlify** - Free tier available
- **Vercel** - Free static hosting

### Paid Options:
- **AWS S3** - Reliable and scalable
- **Azure Blob Storage** - Microsoft ecosystem
- **DigitalOcean Spaces** - Developer-friendly

## Security Considerations

1. **Update server should use HTTPS**
2. **Consider basic authentication** for update files
3. **Monitor download logs** for unauthorized access
4. **Keep backup of previous versions**

## Support

Provide your client with:
1. Installation instructions
2. Troubleshooting guide
3. Contact information for support issues
4. Expected update schedule
