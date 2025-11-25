# Azure CLI - AAD to MS Graph Migration: What Still Needs Work

**Migration Status**: Largely complete (v2.37.0, May 2022)  
**Last Updated**: November 25, 2025

---

## Summary

The migration from AAD Graph (`graph.windows.net`) to Microsoft Graph (`graph.microsoft.com`) is **functionally complete**. Most legacy references are **intentional for backward compatibility** and should NOT be removed.

---

## Code That Still Needs Updates

### 1. Help Text Update (Minor)
**File**: `src/azure-cli/azure/cli/command_modules/synapse/manual/_params.py`  
**Line**: 635  
**Current**: `help='use with --assignee-object-id to avoid errors caused by propagation latency in AAD Graph')`  
**Fix**: Change "AAD Graph" to "Microsoft Graph"  
**Priority**: Low (cosmetic only)

---

## Open GitHub Issues Related to AAD → MS Graph Migration

### Issue #14880: Cannot configure SPA redirect URIs
- **URL**: https://github.com/Azure/azure-cli/issues/14880
- **Status**: OPEN
- **Problem**: `az ad app update` cannot add redirect URIs to Single Page Application (SPA) section - only adds to web section
- **Impact**: Users must use Azure Portal for SPA configurations
- **Needed**: Add `--spa-redirect-uris` parameter to CLI commands

### Issue #20066: `az ad app owner add` requires deprecated AAD Graph permissions
- **URL**: https://github.com/Azure/azure-cli/issues/20066
- **Status**: OPEN
- **Problem**: Command still requires AAD Graph `Application.ReadWrite.OwnedBy` permission, not just MS Graph permission
- **Impact**: Service principals with only MS Graph permissions cannot add app owners
- **Needed**: Verify command uses MS Graph API and accepts MS Graph permissions only

---

## Code References That Are INTENTIONAL (Do Not Change)

These legacy references support **backward compatibility** and should NOT be removed:

| File | Line | Reference | Reason |
|------|------|-----------|--------|
| `src/azure-cli-core/azure/cli/core/cloud.py` | 373-374 | Both `graph.windows.net` and `graph.microsoft.com` endpoints | Supports older cloud configs, AzureStack compatibility |
| `src/azure-cli/azure/cli/command_modules/profile/custom.py` | 21-22 | Maps both `aad-graph` and `ms-graph` | Allows users to reference either endpoint |
| `src/azure-cli-core/azure/cli/core/tests/test_util.py` | 366-377 | Tests for both AAD Graph and MS Graph | Validates backward compatibility |
| `src/azure-cli-testsdk/.../recording_processors.py` | 59 | `graph.windows.net` regex | Supports old test recordings |
| `src/azure-cli/HISTORY.rst` | Various | Historical AAD Graph references | Accurate historical documentation |

---

## Test Recordings (No Action Needed Now)

Multiple test YAML files contain old AAD Graph recordings (`azure-graphrbac/0.60.0`, `graph.windows.net`). These are **functional** and only need regeneration when the related commands are updated in the future.

---

## Quick Reference

### Migration Completed
✅ GraphClient implementation (`src/azure-cli/azure/cli/command_modules/role/_msgrpah/_graph_client.py`)  
✅ Core commands migrated to Microsoft Graph  
✅ Documentation (`/doc/microsoft_graph_client.md`)  
✅ Breaking changes announced (v2.37.0, May 2022)

### Still Open
🔧 1 cosmetic help text update (synapse module)  
🔧 2 open GitHub issues (SPA URIs, permission validation)

---

## Conclusion

**Only 1 code change needed**: Update help text in `synapse/manual/_params.py` line 635.

**2 functional issues remain**: GitHub issues #14880 and #20066 require feature work, not migration work.

All other legacy AAD Graph references are intentional for backward compatibility.
