# Stable 2.12 fork: service-specific account lifecycle fix

Base: upstream v2.12.0. Original MIT license and notices remain unchanged.

`WrapperService.SignalStopped` used `ServiceManager.Open()` with its default
SC_MANAGER_ALL_ACCESS. A dedicated virtual service account cannot open SCM
with that access. When a child exits successfully, SignalStopped throws before
Environment.Exit and the wrapper remains running.

Request SC_MANAGER_CONNECT instead, retaining the existing service-specific
QUERY_STATUS access and SetServiceStatus call. No SCM or BBS-account permissions
are broadened. Native integration tests live in geos_one/mysticd and cover
Windows 10 x86/x64, MIS POP3, graceful SCM stop, clean child exit, and actual
virtual-account positive/negative file access.

The workflow builds the .NET Framework 4.6.2 standalone binary with the upstream
ILMerge target. It retains dependency license notices. Installer pinning follows
only after native tests pass; this fork does not imply upstream endorsement.

The selected Framework target uses log4net 3.3.0 because NuGet audit rejects
upstream 2.0.12 for GHSA-4f7c-pmjv-c25w (log-event loss with forbidden XML
characters). 3.3.0 requires Framework 4.6.2; audit remains enabled and warnings
remain errors. Legacy upstream targets are outside this fork's release build.

The release folder includes the original WinSW MIT license, log4net Apache
license/notice (source commit 42a77cb05abfd5b1ad7224f0861a932eac95c7c8), and
YamlDotNet v8.1.2 MIT notice. Only the net462 target is built for this installer.
Native-tested CI output is promoted unchanged to the release; do not replace
assets under an already pinned release tag.
