v3.0.0 2017-05-11
-----------------

- Split up OPAM packages into multiple independent ones. We now
  have a standalone `cstruct`, and then separate `cstruct-lwt`,
  `cstruct-async`, `cstruct-unix` packages, and a `ppx_cstruct`
  package for the syntax extension. Transitional findlib packages
  with the old scheme are available, but now packages should migrate
  to using `cstruct-async` instead of `cstruct.async` for example.
  This has the added benefit of the OPAM package names now matching
  the findlib names. (#138 by @avsm @rgrinberg).

- Port build to [jbuilder](https://github.com/janestreet/jbuilder).
  See the README for local development instructions.

- Ensure that `check_alignment` only takes a non-zero argument
  for alignment (#143 #145 by @cfcs @avsm).

v2.4.1 2017-05-03
-----------------

- fix missing `ppx_tools_versioned` dependency (#136, @let-def)

v2.4.0 2017-03-30
-----------------

Distribute the PPX extension so that it is compatible with Jbuilder.
`ppx_cstruct` is now distributed as both a library and a binary.
Findlib predicates are used to distinguish usage:
 - the binary is used for toplevel and simple -ppx building
 - the library is used for linking custom rewriters
 - `-package cstruct.ppx` alone uses the binary for rewriting.
 - `-package cstruct.ppx` -predicates custom_ppx,ppx_driver" is used to link the rewriter.

To use the PPX extension in jbuilder, just add:

```
  (libraries (cstruct))
  (preprocess (pps (cstruct.ppx)))
```

to your `jbuild` file.  This may be renamed to `ppx_cstruct` in a
future release so that the PPX dependency is decoupled from the main
library, so this `cstruct.ppx` is intended to be transitional as it
is what was originally used.

v2.3.3 2017-03-28
-----------------

* Port ppx extension to use `ocaml-migrate-parsetree` so it should
  also compile on future revisions of OCaml (#127 via @let-def).

v2.3.2 2017-03-03
-----------------

* Add support for OCaml 4.05 for the PPX extension.
* Docs: correct to description of shift function (#121 via @orbifx).

v2.3.1 2016-12-10
-----------------

* Fix a memory leak in the exception printing code (#130 via @djs55)
* Appveyor CI fixes (#130 via @avsm)
* Fix typo in docstring (#117 via @yomimono)
* Fix opam base-unix dependency (#115 via @avsm)

v2.3.0 2016-08-16
-----------------

* Add `Cstruct.of_bytes/to_bytes`. In common with the existing
  implementation, this relies on the representation of bytes and string
  being the same, which is true as of OCaml 4.04 and lower (#105 via @yallop).
* Support OCaml 4.04 (#111 via @gasche).

v2.2.0 2016-06-30
-----------------

* Make `create` zero out the new buffer. The new `create_unsafe`
  function can be used if you want to trade safety for speed.

v2.1.0 2016-05-04
-----------------

* Add `hexdump_pp` that uses the Format module. This works better with the
  Logs library than using `hexdump_to_buffer`, and also makes it easy to
  indent the hexdump (#100 via @talex5).

v2.0.0 2016-04-26
-----------------

* Remove camlp4 extension as it is no longer maintained (#95).
* Add support for OCaml 4.03 in the PPX extension (#96).
* Minimum supported OCaml version for the library is now 4.02.3.
* Fix parsing of high int32 `@@enum` values.
* Move `Cstruct.check_alignment` into the stubs. Before this patch
  we returned the buffer address from C and then calculated using
  OCaml's boxed `Int64.t`. This patch reduces minor allocations by
  performing the calculation in the C stubs. This makes the function
  suitable for use in an assert in a performance sensitive path.

v1.9.0 2016-02-19
-----------------

* Add support for a ppx-based extension that uses the extension point
  support in OCaml 4.02 and higher to generate Cstruct and Cenum
  function definitions.  The new syntax is documented in the README file.

v1.8.0 2016-01-05
-----------------

* Add support for `-safe-string` in OCaml 4.02 upwards.
  The main change is to rename `blit_to_string` to `blit_to_bytes` and
  change its type so that it writes to bytes rather than string
  (#74 by @yallop).
* Remove strong build-time dependency on `camlp4` in the base library.
  The `sexplib` functions were only used in the interface, so replace them
  with manually written ones.  This also enables compatibility with latest
  Core that has switched to ppx.
* Add multi-distro testing via Travis/Docker containers.

v1.7.1 2015-12-15
-----------------

* Correct error output for `LE.get_uint16` on invalid bounds (#75)
* Fix `fillv`. If the source didn't fit in the buffer then we
  skipped the amount we wanted to copy, not the amount actually copied (#77).

v1.7.0 2015-07-11
-----------------

* Add `Cstruct.concat` and `Cstruct.append` (#57, @pqwy)
* Add `js_of_ocaml` stubs (#63, #64, @djs55)

v1.6.0 2015-03-28
-----------------

* Add `memset` to set all the bytes of a cstruct value efficiently (#49)
* More useful `Invalid_argument` parameters (#48).
* Fix `to_sexp` to expose only the current view (#44 from David Kaloper).
* Add `compare` and `equal` (#23, #24 and #45 from David Kaloper).
* Add `fillv` to copy over a list of buffers (from Thomas Leonard).
* Shift to centralised Travis scripts.

v1.5.0 2014-11-24
-----------------

* Make `camlp4` an optional build-time dependency (#35).
* Remove `ounit` as a dependency in the `opam` file.
* Improve `opam` description file for OPAM 1.2 workflow (#36).
* Refresh Merlin IDE description (#37).

v1.4.0 2014-08-10
-----------------

Comprehensive addition of bounds checking to all cstruct operations
(from @pqwy in #33).  The major changes are:
* Disallow negative indexing with all cstruct accessors.
* Disallow negative `sub` and `shift` operations.
* Make sure `of_bigarray` cannot create invalid `cstruct` values.

v1.3.1 2014-07-10
-----------------

* Also bounds test single-byte operations on views (#31 via @pqwy).

v1.3.0 2014-07-04
-----------------

* Add bounds checks for `Cstruct.BE/LE` functions that violate a view.
  Previously, only bounds errors on the underlying buffers would raise.
  Bug #25, reported by Mindy Preston in mirage/mirage-tcpip#56.
* Add 'Lwt_cstruct.complete' to ensure that `read`/`write` operatiosn
  run to completion.
* Add `Sexplib` conversion functions to `Cstruct.t` values (#27 #22).

v1.2.0 2014-06-06
-----------------

Add a `sexp` optional decorator to `cenum` to output the values as s-expressions.
This is compatible with the `sexplib` convention.  The syntax is;

```
cenum foo64 {
  ONE64;
  TWO64;
  THREE64
} as uint64_t(sexp)
```

And `sexp_of_foo64` and `foo64_of_sexp` functions will also be available.
The representation of the Sexp is the string representation of the enum.

v1.1.0 2014-02-19
-----------------

* Improve bounds checks on sub, shift, set_len, add_len.
* Add `to_bigarray` to convert back into a Bigarray slice.

v1.0.1 2013-12-09
-----------------

* Fix Cstruct.shift function

v1.0.0 2013-12-05
-----------------

* Remove IPv4/IPv6 types (now moved to `ocaml-ipaddr`).
* Improved ocamldoc for the interface.
* More conservative bounds checking in the length manipulation functions.
* Build C stubs with `-Wall`.

v0.8.1 2013-11-06
-----------------

* Trailing semicolons are allowed in cstruct field definitions.
* Buffer elements can be any primitive integer, not just `uint8`.

v0.8.0 2013-10-13
-----------------

* Improved ocamldoc for BE/LE modules.
* Add Travis-CI test scripts and fix `test.sh` script compilation.
* Support int32/int64 constant values in cenum like `VAL = 0xffffffffl`, useful for 32-bit hosts.
* Check and raise error in case of negative offsets for blits (#4).
* Correctly preserve the sequence after a constant constructor is set during a `cenum` definition.
* Do not repeat the `sizeof_<field>` binding for every get/set field (should be no externally observable change).
* Add `Cstruct.hexdump_to_buffer` to make spooling hexdump output easier.
* Generate `hexdump_foo` and `hexdump_foo_to_buffer` prettyprinting functions for a `cstruct foo`.

v0.7.1 2013-03-06
-----------------

* Add `Async_cstruct.Pipe` to map pipes of `Cstruct` buffers to strings or `Bigsubstring`.

v0.7.0 2013-02-25
-----------------

* Add zero-copy conversion functions to/from the Core `Bigsubstring`.
* Add an `of_string` function to simplify the construction from OCaml values.
* Add Async interface to interoperate with Jane Street Core code.

v0.6.2 2013-02-08
-----------------

* Add experimental `cstruct.obuild` for the `obuild` build tool.
* Use bounds checked version of all functions in the external interface.
* Expose the `Cstruct.debug` to dump internal state of a buffer to a string.
* Add `set_len` and `add_len` to manipulate the total-length field directly.

v0.6.1 2012-12-20
-----------------

* Add `sendto`, `read` and `recvfrom` functions to the Lwt subpackage.

v0.6.0 2012-12-20
-----------------

* Add fast bigarray<->string functions to replace byte-by-byte copies.
* Add an Lwt sub-package to expose a write call.
* Depend on ocplib-endian for fast low-level parsing of integers.
* Make `Cstruct.t` a record type that doesn't use Bigarray slicing
  to provide views onto buffers. This lets views be allocated directly
  on the minor heap rather than forcing a major heap allocation. It
  does alter the external API, so previous users of cstruct wont work.

v0.5.3 2012-12-16
-----------------

* No functional changes, just OASIS packaging fix to right version.

v0.5.2 2012-12-11
-----------------

* Remove the separate `xen` and `unix` subdirectories, as the
  portable `Bigarray` is now provided by the `xenbigarray` package.

v0.5.1 2012-09-28
-----------------

* Add `string_to_<cenum>` function to match the `<cenum>_to_string`,
  primarily to help with command-line parsing of enum arguments.

v0.5.0 2012-09-20
-----------------

* Add a signature generator for cstruct and cenum to permit their use in `.mli` files.
* Use the more reliable revised syntax camlp4 quotation expander, to avoid
  broken AST output from antiquotations.
* Switch the `xen/` version over to using OASIS also.

v0.4.0 2012-09-02
-----------------

* Fix META file for use with Xen

v0.3 2012-08-25
--------------

* Initial public release
