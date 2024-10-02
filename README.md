# OpenAPI Specification Collection

This repository contains a variety of valid and invalid OpenAPI Specification (OAS) and Swagger files. The idea is that these files can be used to validate OAS parser libraries and/or other tools that process these types of documentations.

For every OpenAPI Specifcation there is a matching Gloo Edge Portal APIDoc which has the content of the matching OAS/Swagger file inlined. This allows us to easily test how Gloo Edge Portal process these types of documents.

## OAS/Swagger files

This repository contains the following list of files. With each file we will describe the problem/issue with the given file, how the online Swagger parser at https://apitools.dev/swagger-parser/online/ parses the given file and how Gloo Edge Portal parses the file.

----

__`oas-3_0_1-valid.yaml`__

__Issue__: No issue, valid OAS 3.0.1 file

__Online Swagger Parser__: valid

__Gloo Edge__: Valid. Deploys successfully

----

__`oas-3_0_1-first-path-wrong-indentation.yaml`__

__Issue__: The first path in the spec has an incorrect indentation.

__Online Swagger Parser__: 
```
bad indentation of a mapping entry at line 63, column 3: 
      /pets/{petId}:
      ^
```

__Gloo Edge__: 
```
status:
    reason: 'error converting YAML to JSON: yaml: line 62: did not find expected key'
    state: Invalid
```

----

__`oas-3_0_1-last-path-wrong-indentation.yaml`__

__Issue__: The last path in the spec has an incorrect indentation.

__Online Swagger Parser__:
```
duplicated mapping key at line 64, column -672:
        get:
        ^
```

__Gloo Edge__: Gloo Edge seems to parse the invalid file, omitting the path with the wrong indentation. This should not happen, invalid spec files should be rejected.
```
status:
  displayName: Swagger Petstore
  observedGeneration: 1
  openApi:
    operations:
    - operationId: createPets
      path: /v1/pets
      summary: Create a pet
      verb: POST
    - operationId: showPetById
      path: /v1/pets
      summary: Info for a specific pet
      verb: GET
  state: Succeeded
  version: 1.0.0
```

----

__`oas-3_0_1-only-path-wrong-indentation.yaml`__

__Issue__: The openapi spec file only has a single path, and this path has the wrong indentation.

__Online Swagger Parser__: 
```
Swagger schema validation failed.
  #/paths must NOT have additional properties
  #/paths must NOT have additional properties
  #/paths/~1pets must be object
  ```

__Gloo Edge__: Crashes the Portal Server controller, which goes into `CrashLoopBackOff`, showing the following error in the logs
```
{"level":"info","ts":"2024-10-02T11:18:21.544Z","caller":"controller/controller.go:115","msg":"Observed a panic in reconciler: runtime error: invalid memory address or nil pointer dereference","controlle │
│ panic: runtime error: invalid memory address or nil pointer dereference [recovered]                                                                                                                         │
│     panic: runtime error: invalid memory address or nil pointer dereference                                                                                                                                 │
│ [signal SIGSEGV: segmentation violation code=0x1 addr=0x0 pc=0x3306b7c]                                                                                                                                     │
│                                                                                                                                                                                                             │
│ goroutine 120 [running]:                                                                                                                                                                                    │
│ sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller).Reconcile.func1()                                                                                                                      │
│     /home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.16.3/pkg/internal/controller/controller.go:116 +0x30a                                                                                         │
│ panic({0x3978260?, 0x5802e40?})                                                                                                                                                                             │
│     /opt/hostedtoolcache/go/1.21.10/x64/src/runtime/panic.go:920 +0x290                                                                                                                                     │
│ github.com/solo-io/dev-portal/pkg/routing/parser/openapi.parseOperations({0xc000f464e0, 0xc000f464e0}, 0xc000f464e0, 0xc0010b44f8)                                                                          │
│     /home/runner/work/dev-portal/dev-portal/pkg/routing/parser/openapi/openapi_parser.go:138 +0x3dc                                                                                                         │
│ github.com/solo-io/dev-portal/pkg/routing/parser/openapi.(*openApiParser).ParseOperations(0xc00035ec90, {0x4174348, 0xc000886cd0}, 0xc000f464e0)                                                            │
│     /home/runner/work/dev-portal/dev-portal/pkg/routing/parser/openapi/openapi_parser.go:100 +0xe5                                                                                                          │
│ github.com/solo-io/dev-portal/pkg/controllers/portal/reconcilers/apidoc.(*apiDocSyncer).addOpenAPIDocInfoToStatus(0xc00089e600, 0xc0010b49c8, 0xc0005de3c0, {0xc000f42900, 0x81f, 0x900}, 0x0)              │
│     /home/runner/work/dev-portal/dev-portal/pkg/controllers/portal/reconcilers/apidoc/apidoc_syncer.go:159 +0x1ce                                                                                           │
│ github.com/solo-io/dev-portal/pkg/controllers/portal/reconcilers/apidoc.(*apiDocSyncer).makeAPIDocStatus(0xc00089e600, 0xc0005de3c0)                                                                        │
│     /home/runner/work/dev-portal/dev-portal/pkg/controllers/portal/reconcilers/apidoc/apidoc_syncer.go:140 +0xaf1                                                                                           │
│ github.com/solo-io/dev-portal/pkg/controllers/portal/reconcilers/apidoc.(*apiDocSyncer).SyncAPIDoc(0xc00089e600, 0xc0005de3c0)                                                                              │
│     /home/runner/work/dev-portal/dev-portal/pkg/controllers/portal/reconcilers/apidoc/apidoc_syncer.go:48 +0x65                                                                                             │
│ github.com/solo-io/dev-portal/pkg/api/portal.gloo.solo.io/v1beta1/controller.genericAPIDocSyncer.Sync({{0x4174348, 0xc000886cd0}, {0x4154170, 0xc00089e600}}, {0x418b2e8, 0xc0005de3c0})                    │
│     /home/runner/work/dev-portal/dev-portal/pkg/api/portal.gloo.solo.io/v1beta1/controller/reconciler_impl.go:330 +0xe6                                                                                     │
│ github.com/solo-io/dev-portal/codegen/lib/reconciler/base.(*reconciler).Reconcile(0xc00089e680, {0x418b2e8, 0xc0005de3c0})                                                                                  │
│     /home/runner/work/dev-portal/dev-portal/codegen/lib/reconciler/base/reconciler.go:192 +0x424                                                                                                            │
│ github.com/solo-io/skv2/pkg/reconcile.(*runnerReconciler).Reconcile(0xc000885440, {0x4174310, 0xc000d19d70}, {{{0xc000a694f0, 0x7}, {0xc000a694e0, 0xf}}})                                                  │
│     /home/runner/go/pkg/mod/github.com/solo-io/skv2@v0.36.3/pkg/reconcile/runner.go:206 +0xd52                                                                                                              │
│ sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller).Reconcile(0xc0008b6fa0, {0x4174310, 0xc000d19d70}, {{{0xc000a694f0, 0x7}, {0xc000a694e0, 0xf}}})                                       │
│     /home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.16.3/pkg/internal/controller/controller.go:119 +0x1be                                                                                         │
│ sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller).reconcileHandler(0xc0008b6fa0, {0x4174310, 0xc000d19d70}, {0x3acdd00, 0xc000e825c0})                                                   │
│     /home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.16.3/pkg/internal/controller/controller.go:316 +0x4b9                                                                                         │
│ sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller).processNextWorkItem(0xc0008b6fa0, {0x4174348, 0xc0005faa00})                                                                           │
│     /home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.16.3/pkg/internal/controller/controller.go:266 +0x3fc                                                                                         │
│ sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller).Start.func2.2()                                                                                                                        │
│     /home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.16.3/pkg/internal/controller/controller.go:227 +0xdf                                                                                          │
│ created by sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller).Start.func2 in goroutine 146                                                                                                │
│     /home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.16.3/pkg/internal/controller/controller.go:223 +0x98a
```

----

__`oas-3_0_1-operationId-wrong-indentation.yaml`__

__Issue__: One of the `operationId` fields has the wrong indentation.

__Online Swagger Parser__: 
```
bad indentation of a mapping entry at line 13, column 20:
            operationId: listPets
                       ^
```

__Gloo Edge__: 
```
status:
  reason: 'error converting YAML to JSON: yaml: line 13: mapping values are not allowed
    in this context'
  state: Invalid
```

----

__`oas-3_0_1-parameters-wrong-indentation.yaml`__

__Issue__: One of the `parameters` fields has the wrong indentation.

__Online Swagger Parser__: 
```
bad indentation of a mapping entry at line 16, column 9:
            parameters:
            ^
```

__Gloo Edge__: 
```
status:
  reason: 'error converting YAML to JSON: yaml: line 15: did not find expected ''-''
    indicator'
  state: Invalid
```

----

__`swagger-invalid-responses.yaml`__

__Issue__: Responses in the Swagger file should have at least on property when specified.

__Online Swagger Parser__: 
```
Swagger schema validation failed.
  #/paths/~1status~1{code}/get/responses must NOT be valid
  #/paths/~1status~1{code}/get/responses must NOT have fewer than 1 properties
  #/paths/~1get/get/responses must NOT be valid
  #/paths/~1get/get/responses must NOT have fewer than 1 properties
  #/paths/~1post/post/responses must NOT be valid
  #/paths/~1post/post/responses must NOT have fewer than 1 properties
  ...
```

__Gloo Edge__: Gloo Edge parses the invalid file. This should not happen, invalid spec files should be rejected.
```
status:
  description: API Management facade for a very handy and free online HTTP tool.
  displayName: httpbin.org
  ...
  state: Succeeded
  version: "1.0"
```