# 🛡️ zero-trust-file-security

**zero-trust-file-security** implements a robust zero-trust architecture for file system access. By defaulting to a strict "deny-all" policy, this framework protects sensitive directories from unauthorized viewing, modification, or ransomware attacks, requiring explicit authorization for every read, write, and execute request.

Powered by the [EaseFilter File Security SDK](https://github.com/EaseFilterSDK), this project gives you kernel-level, high-performance file interception and access control.

## 🏗️ Architecture

The following diagram illustrates how policy rules in user mode are enforced down at the Windows Kernel level, intercepting all I/O requests before they reach the file system:

![Zero-Trust Architecture](https://www.easefilter.com/images/zero-trust-architecture.jpg)

## 🖥️ Demo Interface

Below is an example of the zero-trust filter service dashboard, showcasing how to dynamically handle and display file-access rule exceptions for processes and users:

![Zero-Trust Demo UI](https://www.easefilter.com/images/zero-trust-demo-screenshot.jpg)

## ✨ Core Features

* **Default Deny (Zero-Trust):** All users, applications, and network requests are blacklisted by default.
* **Process-Level Whitelisting:** Explicitly authorize trusted applications (e.g., `notepad.exe`, `your-app.exe`) to access specific files.
* **Granular Access Rights:** Define exact permissions (Read, Write, Delete, Rename, Execute) on a per-user or per-process basis.
* **Ransomware & Malware Prevention:** Untrusted processes cannot encrypt, delete, or exfiltrate protected files out of the box.
* **Real-Time Auditing:** Comprehensive logging of all allowed and denied file I/O operations for compliance and security monitoring.

## 📄 Implement Zero trust file access control with EaseFilter
[EaseFilter File Control Filter Driver](https://www.easefilter.com/kb/file-control-file-security-sdk.htm) allows you to control the file I/O operations with the filter rule configuration by setting the whitelist and blacklist processes or users, you can allow or block the specific file I/O operation to the specific process or user, you can control who can read your file, allow or block the file modification, prevent your important file from being deleted, renamed.

To implement the Zero Trust file access control , you can setup the filter rule with the default least privilege access rights, by default all the processes or users don’t have privilege to access the files inside this filter rule, it is zero trust to all processes and users. You can setup the whitelist for the filter rule, to add the specific access rights to specific processes or users, so the processes or users who are in the whitelist can have the specific access rights to the files.

[EaseFilter Encryption Filter Driver(EEFD)](https://www.easefilter.com/kb/transparent-file-encryption-filter-driver-sdk.htm) allows you to encrypt file automatically and transparently, combine with the File Control Filter Driver and [Process Filter Driver](https://www.easefilter.com/kb/process-filter-driver-sdk.htm), you can implement the Zero Trust File Access Control with encryption enabled, it can enhance the file security. By default all files will be encrypted automatically, all processes or users can't read the encrypted files, they will get the raw encrypted data. You can authorize the processes or users to access these encrypted files. 


## ⚙️ Installation

1. Clone this repository to your local machine:
   ```bash
   git clone https://github.com/EaseFilterSDK/Zero-Trust-File-Security.git
   ```
2. Open the solution in Visual Studio.
3. Ensure the `EaseFilter.FilterControl.dll` is properly referenced in your project.
4. Build the solution. *Note: You will need to run the application with Administrator privileges to load the filter driver.*

## 🚀 Getting Started

The core concept of this project is to deny all access by default and explicitly grant permissions to trusted processes.

The following example demonstrates how to protect a directory (`C:\SecureData\`) so that absolutely no user or program can read or modify the files—except for `MySecureApp.exe`.

### Example: Whitelisting a Specific Process

```csharp
using System;
using EaseFilter.FilterControl;

class Program
{
    static void Main(string[] args)
    {
        // 1. Initialize the Filter Control
        FilterControl filterControl = new FilterControl();

        // 2. Define the protected directory
        string protectedPath = @"C:\SecureData\*";
        FileFilter rule = new FileFilter(protectedPath);

        // 3. ZERO TRUST: Deny all file access by default
        // This blocks read, write, delete, and execution for everyone
        rule.AccessFlag = FilterAPI.ALLOW_MAX_RIGHT_ACCESS 
						& ~FilterAPI.ALLOW_FILE_WRITE
                        & ~FilterAPI.ALLOW_FILE_DELETE;
						& ~FilterAPI.ALLOW_FILE_READ						
        	
		// 4. WHITELIST: Grant full access ONLY to 'MySecureApp.exe'
		// EXPLICIT AUTHORIZATION WITH CRYPTOGRAPHIC VERIFICATION
		// Trust based on Digital Certificate (Best for commercial software)
		// If a rogue MySecureApp.exe tries to access the files but isn't signed 
		// by the real 'EaseFilter Technologies', it will be blocked instantly.
		rule.AddTrustedProcessRight(
			(uint)FilterAPI.ALLOW_MAX_ACCESS_RIGHT, 
			"MySecureApp.exe", 
			"EaseFilter Technologies", // Certificate Subject Name
			""                       // Hash (omitted)
		);

        // 5. Add the rule and start the engine
        filterControl.AddFilter(rule);
        
        try
        {
            filterControl.StartFilter();
            Console.WriteLine("Zero-Trust File Security is active.");
            Console.WriteLine("Try opening files in C:\\SecureData\\ with anything other than MySecureApp!");
            Console.ReadLine();
        }
        catch (Exception ex)
        {
            Console.WriteLine("Failed to start filter: " + ex.Message);
        }
        finally
        {
            // Ensure the filter stops securely when the app closes
            filterControl.StopFilter();
        }
    }
}
```



## EaseFilter File System Filter Driver SDK Reference
| Product Name | Description |
| --- | --- |
| [Cloud File System SDK](https://www.easefilter.com/cloud/cloud-file-system-sdk.htm) | EaseFilter Cloud File System SDK Introduction. |
| [CloudTier Storage Tiering SDK](https://www.easefilter.com/cloud/storage-tiering-sdk.htm) | EaseFilter Storage Tiering Filter Driver SDK Introduction. |
| [File Monitor SDK](https://www.easefilter.com/kb/file-monitor-filter-driver-sdk.htm) | EaseFilter File Monitor Filter Driver SDK Introduction. |
| [File Control SDK](https://www.easefilter.com/kb/file-control-file-security-sdk.htm) | EaseFilter File Control Filter Driver SDK Introduction. |
| [File Encryption SDK](https://www.easefilter.com/kb/transparent-file-encryption-filter-driver-sdk.htm) | EaseFilter Transparent File Encryption Filter Driver SDK Introduction. |
| [Registry Filter SDK](https://www.easefilter.com/kb/registry-filter-drive-sdk.htm) | EaseFilter Registry Filter Driver SDK Introduction. |
| [Process Filter SDK](https://www.easefilter.com/kb/process-filter-driver-sdk.htm) | EaseFilter Process Filter Driver SDK Introduction. |
| [EaseFilter SDK Programming](https://www.easefilter.com/kb/programming.htm) | EaseFilter Filter Driver SDK Programming. |

## EaseFilter SDK Sample Projects
| Sample Project | Description |
| --- | --- |
| [CloudTier Storage Tiering Demo](https://www.easefilter.com/cloud/cloudtier-storage-tiering-demo.htm) | A HSM File System Filter Driver Demo. |
| [CloudTier S3 Tiering Demo](https://www.easefilter.com/cloud/cloudtier-s3-intelligent-tiering-demo.htm) | CloudTier S3 Intelligent Tiering Demo. |
| [Cloud File DR S3 Demo](https://www.easefilter.com/cloud/cloud-file-dr-demo.htm) | Cloud File DR S3 Demo. |
| [Amazon S3 File Explorer Demo](https://www.easefilter.com/cloud/s3-browser-demo.htm) | Amazon S3 File Explorer Demo. |
| [Auto File DRM Encryption](https://www.easefilter.com/kb/auto-file-drm-encryption-tool.htm) | Auto file encryption with DRM data embedded. |
| [Transparent File Encrypt](https://www.easefilter.com/kb/AutoFileEncryption.htm) | Transparent on access file encryption. |
| [Secure File Sharing with DRM](https://www.easefilter.com/kb/DRM_Secure_File_Sharing.htm) | Secure encrypted file sharing with digital rights management. |
| [File Monitor Example](https://www.easefilter.com/kb/file-monitor-demo.htm) | Monitor file system I/O in real time, tracking file changes. |
| [File Protector Example](https://www.easefilter.com/kb/file-protector-demo.htm) | Prevent sensitive files from being accessed by unauthorized users or processes. |
| [FolderLocker Example](https://www.easefilter.com/kb/FolderLocker.htm) | Lock file automatically in a FolderLocker. |
| [Process Monitor](https://www.easefilter.com/kb/Process-Monitor.htm) | Monitor the process creation and termination, block unauthorized process running. |
| [Registry Monitor](https://www.easefilter.com/kb/RegMon.htm) | Monitor the Registry activities, block the modification of the Registry keys. |
| [Secure Sandbox Example](https://www.easefilter.com/kb/Secure-Sandbox.htm) |A secure sandbox example, block the processes accessing the files out of the box. |
| [FileSystemWatcher Example](https://www.easefilter.com/kb/FileSystemWatcher.htm) | File system watcher, logging the file I/O events. |
| [ZeroTrust Example](https://www.easefilter.com/kb/zero-trust-file-access-control-demo.htm) | Zero trust file access control with encryption feature. |

## Filter Driver Reference

* [Understand MiniFilter Driver](https://www.easefilter.com/kb/understand-minifilter.htm)
* [Understand File I/O](https://www.easefilter.com/kb/File_IO.htm)
* [Understand I/O Request Packets(IRPs)](https://www.easefilter.com/kb/understand-irps.htm)
* [Filter Driver Developer Guide](https://www.easefilter.com/kb/DeveloperGuide.htm)
* [MiniFilter Filter Driver Framework](https://www.easefilter.com/kb/minifilter-framework.htm)
* [Isolation Filter Driver](https://www.easefilter.com/kb/Isolation_Filter_Driver.htm)

## Support
If you have questions or need help, please contact support@easefilter.com 

## License

This repository contains two distinct licensing categories:

1. **Wrapper/Implementation Code (MIT License):** 
   The source code, scripts, and configuration files provided directly in this repository are licensed under the [MIT License](LICENSE). You are free to modify, distribute, and use this specific integration code.

2. **EaseFilter SDK (Commercial License):**
   This project requires the EaseFilter SDK (including its binaries, filter drivers, and core libraries) to function. The EaseFilter SDK is **proprietary commercial software** and is strictly excluded from the MIT License. 
   
   To use the EaseFilter SDK for commercial purposes or in a production environment, you must obtain a valid commercial license directly from [EaseFilter](https://www.easefilter.com/License.pdf).

[Home](https://www.easefilter.com/) | [Solution](https://www.easefilter.com/solutions.htm) | [Download](https://www.easefilter.com/download.htm) | [Demos](https://www.easefilter.com/online-fileio-test.aspx) | [Blog](https://blog.easefilter.com/) | [Programming](https://www.easefilter.com/kb/programming.htm)
