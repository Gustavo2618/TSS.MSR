# TSS.CPP TODO and follow-up inventory

Reviewed: 2026-11-28

This inventory records the first-party TODOs and related implementation gaps found in `TSS.CPP`. The next session should start with item 1.

## Recommended starting point

1. **Fix and test `TpmStructure::operator<`.** [`include/TpmStructure.h:46`](./include/TpmStructure.h#L46) currently returns `true` for every comparison. Define a valid ordering that satisfies the strict-weak-ordering requirements before this operator is used with ordered STL containers or algorithms.

## Remaining source TODOs and functional gaps

2. **Handle-copy tracking in the dispatcher.** [`Src/Tpm2.cpp:489`](./Src/Tpm2.cpp#L489) describes the general problem: dispatcher-owned handle copies do not automatically update application handles. [`Src/Tpm2.cpp:588`](./Src/Tpm2.cpp#L588) still flags this for `HierarchyChangeAuth`. The original-handle update for `NV_ChangeAuth` has been implemented in its synchronous and asynchronous paths; do not treat that specific behavior as still missing.

3. **Reassess the `NV_ChangeAuth` session TODO.** [`Src/Tpm2.cpp:519`](./Src/Tpm2.cpp#L519) says the auth update needs to change for sessions to work. The existing policy-session `NV_ChangeAuth` sample passed on the simulator, so first identify which session type or scenario this TODO refers to and add a focused test before changing the implementation.

4. **Complete `ObjectChangeAuth` auth tracking.** [`Src/Tpm2.cpp:602`](./Src/Tpm2.cpp#L602) marks the current update as nonfunctional; [`Src/Tpm2.cpp:603`](./Src/Tpm2.cpp#L603) proposes tracking the new auth by a digest of `TpmPrivate` and applying it during `Load()` processing.

5. **Fix `PCR_SetAuthValue` handle updates.** [`Src/Tpm2.cpp:609`](./Src/Tpm2.cpp#L609) marks this path as not working because of handle propagation.

6. **Complete `EvictControl` handle metadata updates.** [`Src/Tpm2.cpp:615`](./Src/Tpm2.cpp#L615) marks auth/name updates as incomplete.

7. **Expand `UpdateRespHandle` scenario tests.** [`Src/Tpm2.cpp:795`](./Src/Tpm2.cpp#L795) notes that its scenario coverage is low.

8. **Support non-default RSA public exponents when signing.** [`Src/Crypto.cpp:489`](./Src/Crypto.cpp#L489) hard-codes exponent 65537 while reconstructing the RSA key.

9. **Validate the qualified name in `ValidateCertify`.** [`Src/TpmTypeExtensions.cpp:117`](./Src/TpmTypeExtensions.cpp#L117) notes that only the object's name is currently checked.

10. **Define Unicode behavior for string hashing.** [`Src/TpmTypeExtensions.cpp:481`](./Src/TpmTypeExtensions.cpp#L481), [`include/TpmTypes.h:3263`](./include/TpmTypes.h#L3263), and [`Src/TpmExtensions.cpp.snips:99`](./Src/TpmExtensions.cpp.snips#L99) refer to the same `FromHashOfString` issue. Decide the input encoding (for example, UTF-8) and update the source snippet/generated output consistently.

11. **Make the Windows TBS backend genuinely asynchronous.** [`Src/TpmDevice.cpp:419`](./Src/TpmDevice.cpp#L419) notes that a thread will be needed; `Tbsip_Submit_Command` currently blocks in `DispatchCommand`.

12. **Implement or remove the unsupported `PolicySecret` callback path.** [`Src/TpmPolicy.cpp:555`](./Src/TpmPolicy.cpp#L555) reaches `_ASSERT(FALSE)` when `CallbackNeeded` is true. `SetAuthorizingObjectHandle` is the available workaround when the caller already has the handle.

13. **Implement and test `PolicyTicket`.** [`include/TpmPolicy.h:547`](./include/TpmPolicy.h#L547) marks it untested/potentially incomplete; [`Src/TpmPolicy.cpp:585`](./Src/TpmPolicy.cpp#L585) has `_ASSERT(FALSE)` in both digest and execution paths.

14. **Review `AUTH_SESSION` encapsulation and constructors.** [`include/Tss.h:28`](./include/Tss.h#L28) identifies this as API-design debt. Treat it as a broad refactor and check compatibility before changing the public interface.

## Excluded vendor markers

The search also finds 10 `TODO`/`FIXME`/`XXX` matches in the bundled OpenSSL copies under `CryptoEngine/openssl` (x86 and x64). They are upstream/vendor comments or search-pattern false positives, not TSS.CPP-owned backlog items; do not modify them as part of this inventory.

## Separate uncommitted work

At the time of this review, Git also showed changes in `../TssCodeGen/src/CGenCpp.cs`. This is pending generator work, not a TODO marker in the TSS.CPP source inventory; review and commit it separately.
