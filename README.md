# Google Summer of Code 2021

## Project

link: https://summerofcode.withgoogle.com/projects/#6190959651454976

### Title
**Support call hierarchy in Haskell Language Server**

### Description (copy from original details)
This proposal aims to add call hierarchy support in Haskell Language Server(HLS).

Language Server Protocol(LSP) has supported call hierarchy in 3.16, which makes us implement it in HLS possible.

Inspired by this issue, my proposal involves the following steps this summer to contribute to call hierarchy:

1. LSP spec support.
2. prepareCallhierarchy implementation.
3. incomingCalls implementation.
4. outgoingCalls implementation.
5. Test

### Technical details
The promise of this work is that LSP support call hierarchy requests and LSP formulated the procedure of a standard call hierarchy, which includes prepare steps and incoming/outcoming requests.

With the help of LSP, many lsp-client like VSCode, Vim, Emacs can easily support call hierarchy if they have the ability to send these requests and process the responses. The language server, haskell language server in this case, just need to handle corresponding requests and send the result to the client. That means, our server can aim to the language service itself with only a few configurations about handling requests(it is very slight work in most cases).

Thanks to HieDb, we can simply gather module information without analyzing the `.hie` file.

Our service is divided into three methods according to the LSP specification:

1. `textDocument/prepareCallHierarchy`, given a position, return a `CallHierarchyItem` which includes some essential information at this position. The other two requests also rely on this preparation work, no matter incoming or outgoing.
2. `callHierarchy/incomingCalls`, this request includes a `CallHierarchyItem` as the parameter, and we should return a list as result, each element in the list is a `CallHierarchyItem` which called the parameter item.
3. `callHierarchy/outgoingCalls`, same as the previous one, but in the opposite direction.

#### Construct CallHierarchy
1. Get `HieAst` from `IdeState`.
2. Validate the request position and return the error if it is invalid.
3. Get `OccName` and kind info from `OccName`.

#### Process incoming/outgoing calls
1. Get `hiedb` from `ShakeExtras`
2. Query the db to get the graph of the current project.
3. Find the reachable vertexes by analyzing edges and build a `CallHierarchyItem` according to the location.

## Progress
1. - [x] Add support of haskell-lsp. (2021-06-01)
2. - [x] Supply plugin configure. (2021-06-02)
3. - [x] Fundamental test frame. (2021-06-03)
4. - [ ] `prepareCallHierarchy`
    1. - [x] Expected tests, including typeclass, GADTs, typefamily. (2021-06-12)
    2. - [ ] Unexpected tests, including invalid position and invalid file path(scarcely possible in my mind).
    3. - [ ] Construct `CallHierarchyItem`
5. - [ ] `incomingCalls`
    1. - [ ] Corresponding tests.
    2. - [ ] Handle `incomingCalls` request.
6. - [ ] `outgoingCalls`
    1. - [ ] Corresponding tests.
    2. - [ ] Handle `outgoingCalls` request.
