# Reproducing duplicate type checks on initial tsc -b run

Either run `yarn` in `access-source-example` or manually symlink the `a` and `b` directories into `node_modules`. From here on out, everything will be relative to the `access-source-example` directory.

Run `yarn typecheck` or `tsc -b`.
It will output the following:

```
packages/b/b.ts:2:9 - error TS2322: Type 'string' is not assignable to type 'number'.

2   const number: number = "1";
          ~~~~~~

packages/b/b.ts:2:9 - error TS2322: Type 'string' is not assignable to type 'number'.

2   const number: number = "1";
          ~~~~~~


Found 2 errors.

error Command failed with exit code 1.
```

The same typecheck seems to be invoked twice here.

Runing this again, will print out the same. However this time, it will not run any type checking but use the information from the tsbuildinfo files it seems.

Furthermore tsc does not always report this as `2 errors`, depending on how the error has been cached. Follow the next steps:

Fix the type error in `b.ts` and run `yarn typecheck` again.

No error. All declaration files are emitted in `typecheck_cache` directory.

Reintroduce the the type error in `b.ts` run `yarn typecheck` again.

Observe the following output:

```
packages/b/b.ts:2:9 - error TS2322: Type 'string' is not assignable to type 'number'.

2   const number: number = "1";
          ~~~~~~


Found 1 error.

error Command failed with exit code 1.
```

Running `yarn typecheck` again will yield same results.

Delete `typecheck_cache/a.tsbuildinfo`, run `yarn typecheck` again.

Notice the output:

```
$ tsc -b
packages/b/b.ts:2:9 - error TS2322: Type 'string' is not assignable to type 'number'.

2   const number: number = "1";
          ~~~~~~

packages/b/b.ts:2:9 - error TS2322: Type 'string' is not assignable to type 'number'.

2   const number: number = "1";
          ~~~~~~


Found 2 errors.
```

Running `yarn typecheck` again, we then get this output

```
packages/b/b.ts:2:9 - error TS2322: Type 'string' is not assignable to type 'number'.

2   const number: number = "1";
          ~~~~~~


Found 1 error.

error Command failed with exit code 1.
```

Rerunning will result in the same results.
