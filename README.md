Osiris
======

1. 升级支持Python3
1. 给`print`加`()`
1. 删除`z3`文件夹(如果`z3`已安装)
1. 安装`HTMLParser`
    ```
    pip install HTMLParser
    ```
1. 安装HTMLParser
    ```
    pip install HTMLParser
    ```
1. `osiris.py`中`from HTMLParser import HTMLParser`替换为`from html.parser import HTMLParser`

    `disasm_out = disasm_p.communicate()[0]`替换为`disasm_out = disasm_p.communicate()[0].decode('utf-8', 'strict')`

1. `utils.py`中`return solc_p.communicate()[0]`替换为`return solc_p.communicate()[0].decode('utf-8', 'strict')`

    `(int, long)`替换为`six.integer_types`/`int`

    `import six` `import logging` 添加`log = logging.getLogger(__name__)`

1. `source_map.py`中
    ```python
    def __get_source(self):
        fname = self.__get_filename()
        if SourceMap.sources.has_key(fname):
            return SourceMap.sources[fname]
        else:
            SourceMap.sources[fname] = Source(fname)
            return SourceMap.sources[fname]
    ```
    替换为
    ```python
    def __get_source(self): # 两个_
        fname = self.__get_filename()
        if fname not in SourceMap.sources:
            SourceMap.sources[fname] = Source(fname)
        return SourceMap.sources[fname]
    ```
1. `ast_helper.py`中`def extract_state_definitions(self, c_name):`插入`base_contracts = list(base_contracts)`
1. `symExec.py`中
    `for (attr, default) in attr_defaults.iteritems():`替换为`for (attr, default) in six.iteritems(attr_defaults):`,并且`import six`,

    `if visited_edges.has_key(current_edge):`替换为`if current_edge in visited_edges:`

    `temp = long(math.ceil((mem_location + no_bytes) / float(32)))`替换为
    ```python
    if six.PY2:
        temp = long(math.ceil((mem_location + no_bytes) / float(32)))
    else:
        temp = int(math.ceil((mem_location + no_bytes) / float(32)))
    ```

    `temp = long(math.ceil((address + 32) / float(32)))`替换为
    ```python
    if six.PY2:
        temp = long(math.ceil((address + 32) / float(32)))
    else:
        temp = int(math.ceil((address + 32) / float(32)))
    ```

    `temp = long(math.ceil((stored_address + 32) / float(32)))`替换为
    ```python
    if six.PY2:
        temp = long(math.ceil((stored_address + 32) / float(32)))
    else:
        temp = int(math.ceil((stored_address + 32) / float(32)))
    ```

    `temp = long(math.ceil((stored_address + 1) / float(32)))`替换为
    ```python
    if six.PY2:
        temp = long(math.ceil((stored_address + 1) / float(32)))
    else:
        temp = int(math.ceil((stored_address + 1) / float(32)))
    ```

    `input += binascii.unhexlify('%064x' % value)`替换为`input += binascii.unhexlify('%064x' % value).decode('utf-8', 'strict')`

1. `basicblock.py`中`(int, long)`替换为`six.integer_types`, `import six`

1. `taintFlow.py`中的所有取整`/`替换为`//`


======

![](https://img.icons8.com/color/200/000000/osiris.png)

An analysis tool to detect integer bugs in Ethereum smart contracts. Osiris is based on [Oyente](https://github.com/melonproject/oyente).

## Quick Start

A container with the dependencies set up can be found [here](https://hub.docker.com/r/christoftorres/osiris/).

To open the container, install docker and run:

```
docker pull christoftorres/osiris && docker run -i -t christoftorres/osiris
```

To evaluate the SimpleDAO contract inside the container, run:

```
python osiris/osiris.py -s datasets/SimpleDAO/SimpleDAO_0.4.19.sol
```

and you are done!

## Custom Docker image build

```
docker build -t osiris .
docker run -it osiris:latest
```

## Full installation

### Install the following dependencies
#### solc
```
$ sudo add-apt-repository ppa:ethereum/ethereum
$ sudo apt-get update
$ sudo apt-get install solc
```

#### evm from [go-ethereum](https://github.com/ethereum/go-ethereum)

1. https://geth.ethereum.org/downloads/ or
2. By from PPA if your using Ubuntu
```
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository -y ppa:ethereum/ethereum
$ sudo apt-get update
$ sudo apt-get install ethereum
```

#### [z3](https://github.com/Z3Prover/z3/releases) Theorem Prover version 4.6.0.

Download the [source code of version z3-4.6.0](https://github.com/Z3Prover/z3/releases/tag/z3-4.6.0)

Install z3 using Python bindings

```
$ python scripts/mk_make.py --python
$ cd build
$ make
$ sudo make install
```

#### [Requests](https://github.com/kennethreitz/requests/) library

```
pip install requests
```

#### [web3](https://github.com/pipermerriam/web3.py) library

```
pip install web3
```

#### [pysha3](https://github.com/tiran/pysha3) library

```
pip install pysha3
```

### Evaluating Ethereum Contracts

```
#evaluate a local solidity contract
python osiris.py -s <contract filename>
```

Run ```python osiris.py --help``` for a complete list of options.
