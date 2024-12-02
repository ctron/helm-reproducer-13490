# Reproducer for helm/helm#13490

This is a reproducer for [helm/helm#13490](https://github.com/helm/helm/issues/13490).

## With `check-jsonschema`

You can use `check-jsonschema` to validate the file:

```
$ check-jsonschema --schemafile charts/reproducer/values.schema.json charts/reproducer/values.yaml
ok -- validation done
```

Providing an invalid value files results in an error (expected):

```
$ check-jsonschema --schemafile values.schema.json values.yaml
Schema validation errors were encountered.
  values.yaml::$.foo: 'bar' is a required property
  values.yaml::$.foo: Additional properties are not allowed ('baz' was unexpected)
```

## Expectation

I would expect the same with e.g. `helm lint`. However, I get:

```
$ helm lint charts/reproducer
==> Linting charts/reproducer
[INFO] Chart.yaml: icon is recommended
[ERROR] values.yaml: Reference /schemas/bar.json must be canonical
[ERROR] templates/: values don't meet the specifications of the schema(s) in the following chart(s):
reproducer:
Reference /schemas/bar.json must be canonical

Error: 1 chart(s) linted, 1 chart(s) failed
```

Googling for that issue, it seems a way to deal with this is to prefix with `file://`. However, this results in:

```
$ helm lint charts/reproducer
==> Linting charts/reproducer
[INFO] Chart.yaml: icon is recommended
[ERROR] values.yaml: open schemas/bar.json: no such file or directory
[ERROR] templates/: values don't meet the specifications of the schema(s) in the following chart(s):
reproducer:
open schemas/bar.json: no such file or directory

Error: 1 chart(s) linted, 1 chart(s) failed
```
