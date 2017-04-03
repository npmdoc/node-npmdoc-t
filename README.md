# api documentation for  t (v0.5.1)  [![npm package](https://img.shields.io/npm/v/npmdoc-t.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-t) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-t.svg)](https://travis-ci.org/npmdoc/node-npmdoc-t)
#### tree traversal and manipulation

[![NPM](https://nodei.co/npm/t.png?downloads=true)](https://www.npmjs.com/package/t)

[![apidoc](https://npmdoc.github.io/node-npmdoc-t/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-t_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-t/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-t/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-t/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Aaron Stacy",
        "email": "aaron.r.stacy@gmail.com"
    },
    "dependencies": {},
    "description": "tree traversal and manipulation",
    "devDependencies": {
        "chai": "0.1.x",
        "grunt": "~0.4.1",
        "grunt-contrib-jshint": "~0.6.0",
        "grunt-mocha-test": "~0.5.0",
        "grunt-shell": "~0.3.0",
        "mocha": "0.10.x"
    },
    "directories": {},
    "dist": {
        "shasum": "7d9192201ed7f8bbf61598ac73660d8ffcc3b578",
        "tarball": "https://registry.npmjs.org/t/-/t-0.5.1.tgz"
    },
    "engine": "node >= 0.6.0",
    "engines": {
        "node": "*"
    },
    "main": "t.js",
    "maintainers": [
        {
            "name": "aaronj1335",
            "email": "aaron.r.stacy@gmail.com"
        }
    ],
    "name": "t",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "scripts": {
        "test": "grunt test"
    },
    "version": "0.5.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module t](#apidoc.module.t)
1.  [function <span class="apidocSignatureSpan">t.</span>bfs (node)](#apidoc.element.t.bfs)
1.  [function <span class="apidocSignatureSpan">t.</span>dfs (node)](#apidoc.element.t.dfs)
1.  [function <span class="apidocSignatureSpan">t.</span>filter (node)](#apidoc.element.t.filter)
1.  [function <span class="apidocSignatureSpan">t.</span>find ( tree )](#apidoc.element.t.find)
1.  [function <span class="apidocSignatureSpan">t.</span>map ()](#apidoc.element.t.map)
1.  [function <span class="apidocSignatureSpan">t.</span>stroll (tree1, tree2)](#apidoc.element.t.stroll)
1.  object <span class="apidocSignatureSpan"></span>t



# <a name="apidoc.module.t"></a>[module t](#apidoc.module.t)

#### <a name="apidoc.element.t.bfs"></a>[function <span class="apidocSignatureSpan">t.</span>bfs (node)](#apidoc.element.t.bfs)
- description and source-code
```javascript
bfs = function (node) {

    var cur, callback, i, length, par, children,
        isConfigSet = arguments.length === 3,
        config = isConfigSet ? arguments[1] : {},
        queue = isArray(node)? node.slice(0) : [node],
        parents = [undefined],
        childrenName = getChildrenName(config);

    if (node == null) return node;

    if (arguments.length >= 3) {
        config = arguments[1];
        callback = arguments[2];
    } else {
        config = {};
        callback = arguments[1];
    }

    while (queue.length) {
        cur = queue.shift();
        par = parents.shift();
        callback.call(cur, cur, par);
        children = cur[childrenName] || [];
        for (i = 0, length = children.length; i < length; i++) {
            queue.push(children[i]);
            parents.push(cur);
        }
    }

    return node;
}
```
- example usage
```shell
...
the 't' interface is exported in either the browser or node.js. the library
can be installed from [npm](http://search.npmjs.org/#/t):

    $ npm install t

available functions
===================
t.bfs()
-------
perform a breadth-first search, executing the given callback at each node.

t.bfs(node, [config], function(node, par, ctrl) {
    /* ... */
})
...
```

#### <a name="apidoc.element.t.dfs"></a>[function <span class="apidocSignatureSpan">t.</span>dfs (node)](#apidoc.element.t.dfs)
- description and source-code
```javascript
dfs = function (node) {
    var cur, par, children, ctrl, i, ret,
        isConfigSet = arguments.length === 3,
        nodes = isArray(node)? node.slice(0).reverse() : [node],
        config = isConfigSet ? arguments[1] : {},
        callback = arguments[isConfigSet ? 2 : 1],
        parents = [],
        childrenName = getChildrenName(config);
    if (typeof nodes[0] === 'undefined' && nodes.length === 1) return;

    if (config.order === 'post') {
        ret = _dfsPostOrder(nodes, config, callback);
        return isArray(node)? ret : ret[0];
    }


    for (i = nodes.length-1; i >= 0; i--)
        parents.push(undefined);

    while (nodes.length > 0) {
        cur = nodes.pop();
        par = parents.pop();

        ctrl = {};
        callback.call(cur, cur, par, ctrl);

        if (ctrl.stop) break;

        children = (cur && cur[childrenName])? cur[childrenName] : [];

        for (i = ctrl.cutoff? -1 : children.length-1; i >= 0; i--) {
            nodes.push(children[i]);
            parents.push(cur);
        }
    }

    return node;
}
```
- example usage
```shell
...
function to be executed at each node.  the arguments are:
- 'node': the current node
- 'par': the current node's parent
- 'ctrl': control object.  this doesn't currently do anything.

 returns: the first 'node' argument

t.dfs()
-------
perform a depth-first search, executing the given callback at each node.

t.dfs(node, [config], function(node, par, ctrl) {
    /* ... */
})
...
```

#### <a name="apidoc.element.t.filter"></a>[function <span class="apidocSignatureSpan">t.</span>filter (node)](#apidoc.element.t.filter)
- description and source-code
```javascript
filter = function (node) {
    var isConfigSet = arguments.length === 3,
        nodeFactory =  arguments[isConfigSet ? 2 : 1],
        config = isConfigSet ? arguments[1] : {};
    return t.map(node, {
        filter: true,
        childrenName: config.childrenName
    }, nodeFactory);
}
```
- example usage
```shell
...
'map' function takes care of setting children.  the arguments are:
- 'node': the current node
- 'par': the current node's parent. note that this is the parent from
the new tree that's being created.

 returns: a new tree, mapped by the callback function

t.filter()
----------
given a tree, return a tree of the same structure made up of the objects
returned by the callback which is executed at each node.  if, however, at a
given node the callback returns a falsy value, then the current node and all
of its descendents will be pruned from the output tree.

t.filter(node, [config], function(node, par) {
...
```

#### <a name="apidoc.element.t.find"></a>[function <span class="apidocSignatureSpan">t.</span>find ( tree )](#apidoc.element.t.find)
- description and source-code
```javascript
find = function ( tree ) {
    var found,
        isConfigSet = arguments.length === 3,
        callback =  arguments[ isConfigSet ? 2 : 1],
        config = isConfigSet ? arguments[1] : {};
    t.dfs(tree, config, function(node, par, ctrl) {
        if (callback.call(node, node, par)) {
            ctrl.stop = true;
            found = this;
        }
    });

    return found;
}
```
- example usage
```shell
...
you can define the name of the children property with
'config.childrenName'
- 'callback' (last argument):
function to be executed at each node. the arguments are:
- 'node1': the node from the first tree
- 'node2': the node from the second tree

t.find()
----------

given a tree and a truth test, return the first node that responds with a
truthy value

t.find(tree, [config], function(node, par) {
    /* ... */
...
```

#### <a name="apidoc.element.t.map"></a>[function <span class="apidocSignatureSpan">t.</span>map ()](#apidoc.element.t.map)
- description and source-code
```javascript
map = function () {
    var node = arguments[0],
        isConfigSet = arguments.length === 3,
        config = isConfigSet ? arguments[1] : {},
        filter = config.filter,
        nodeFactory = arguments[isConfigSet ? 2 : 1],
        ret = isArray(node)? [] : undefined,
        last = function(l) { return l[l.length-1]; },
        parentStack = [],
        childrenName = getChildrenName(config);

    t.dfs(node, config, function(n, par, ctrl) {
        var curParent = last(parentStack),
            newNode = nodeFactory(n, curParent? curParent.ret : undefined);

        if (filter && ! newNode) {
            ctrl.cutoff = true;
            if (curParent && n === last(curParent.n[childrenName])) {
                parentStack.pop();
                if (curParent.ret[childrenName] &&
                        ! curParent.ret[childrenName].length)
                    delete curParent.ret[childrenName];
            }
            return;
        }

        if (! par) {
            if (isArray(node))
                ret.push(newNode);
            else
                ret = newNode;

        } else {
            curParent.ret[childrenName].push(newNode);

            if (n === last(curParent.n[childrenName])) {
                parentStack.pop();
                if (curParent.ret[childrenName] &&
                        ! curParent.ret[childrenName].length)
                    delete curParent.ret[childrenName];
            }
        }

        if (n[childrenName] && n[childrenName].length) {
            newNode[childrenName] = [];
            parentStack.push({n: n, ret: newNode});
        }
    });

    return ret;
}
```
- example usage
```shell
...
the search, setting the 'cutoff' property of this will not visit any
children of this node
- 'ret': return values of child nodes.  this is only set if 'dfs()' is
called with the 'order' property set to 'post'.

 returns: the first 'node' argument

t.map()
-------
given a tree, return a tree of the same structure made up of the objects
returned by the callback which is executed at each node.  think of the
'underscore''s '_.map()' function, or python's 'map()'

t.map(node, [config], function(node, par) {
    /* ... */
...
```

#### <a name="apidoc.element.t.stroll"></a>[function <span class="apidocSignatureSpan">t.</span>stroll (tree1, tree2)](#apidoc.element.t.stroll)
- description and source-code
```javascript
stroll = function (tree1, tree2) {
    var i, node2,
        isConfigSet = arguments.length === 4,
        callback =  arguments[ isConfigSet ? 3 : 2],
        config = isConfigSet ? arguments[2] : {},
        childrenName = getChildrenName(config),
        nodes2 = isArray(tree2)? tree2.slice(0).reverse() : [tree2],
        len = function(a) { return typeof a === 'undefined'? 0 : a.length; };

    t.dfs(tree1, config, function(node1, par, ctrl) {
        node2 = nodes2.pop();

        callback(node1, node2);

        if (node1 && node2 &&
                len(node1[childrenName]) === len(node2[childrenName]))
            for (i = (node2[childrenName] || []).length-1; i >= 0; i--)
                nodes2.push(node2[childrenName][i]);
        else
            ctrl.cutoff = true;

    });
}
```
- example usage
```shell
...
     arguments are:
     - 'node': the current node
     - 'par': the current node's parent. note that this is the parent from
     the new tree that's being created.

returns: a new tree, filtered by the callback function

t.stroll()
----------

_a walk through the trees..._

given two trees of similar structure, traverse both trees at the same time,
executing the given callback with the pair of corresponding nodes as
arguments.
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
