Create a GitHub Actions workflow job that validates and tests a WinGet manifest for a portable Go CLI application.

Context:

* Application name: kemforge
* Executable: kemforge.exe
* The application is distributed as a ZIP file containing kemforge.exe
* I already have:

  * A public download URL for the ZIP
  * A SHA256 hash of that ZIP
  * A short description of the application
* This repository is used only to prepare and validate WinGet manifests before submitting to microsoft/winget-pkgs

Goal:
The job must:

1. Generate valid multi-file WinGet manifests
2. Validate them
3. Install the app locally using winget
4. Verify that the installed CLI actually runs

Requirements:

1. Job definition:

* Name: test-winget-manifest
* Runs on: windows-latest

2. Inputs (hardcode for now, easy to replace later):

* PackageIdentifier: ConnectingApps.Kemforge
* PackageVersion: 1.3.2
* InstallerUrl: <PUT URL HERE>
* InstallerSha256: <PUT HASH HERE>
* Description: <PUT DESCRIPTION HERE>

3. Create manifest directory structure

4. Generate 3 YAML files:

a) version manifest:

* ManifestType: version
* DefaultLocale: en-US

b) installer manifest:

* InstallerType: zip
* NestedInstallerType: portable
* NestedInstallerFiles:

  * RelativeFilePath: kemforge.exe
* Commands:

  * kemforge

c) locale manifest:

* Include Publisher, PackageName, ShortDescription

Use a recent ManifestVersion:

5. Validate manifest:

* Use winget validate if available
* If not, proceed to install test

6. Install using manifest:

* Run:
  winget install --manifest <path> --silent --accept-package-agreements --accept-source-agreements

7. Verify installation:

* Run:
  kemforge --help
* The step must fail if:

  * command is not found
  * exit code is non-zero

8. Extra validation:

* After download, compute SHA256 again and compare with expected value
* Fail explicitly if mismatch

9. Logging:

* Print clearly:

  * Installer URL
  * SHA256
  * Installation result
  * Output of kemforge --help

10. Output:

* Provide a complete GitHub Actions YAML workflow
* No placeholders except clearly marked ones for URL, hash, description
* Must be directly runnable

Important constraints:

* Do not skip installation test
* Do not assume global PATH is already updated; ensure the command is resolvable
* Fail fast on any mismatch or error
* Keep the workflow deterministic and strict (this is for winget submission readiness)

Nice to have:

* Cache nothing
* Use PowerShell where appropriate
