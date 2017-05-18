Wavedrom
------------------

````
```wavedrom
{ assign:[
  ["out",
    ["|",
      ["&", ["~", "a"], "b"],
      ["&", ["~", "b"], "a"]
    ]
  ]
]}
```
````

```wavedrom
{ assign:[
  ["out",
    ["|",
      ["&", ["~", "a"], "b"],
      ["&", ["~", "b"], "a"]
    ]
  ]
]}
```

### Sphinxの設定

[Wavedrom](http://wavedrom.com/)を使用するには、Sphinx拡張の[sphinxcontrib-wavedrom](https://github.com/bavovanachte/sphinx-wavedrom)をインストールする必要があります。

```
$ pip install sphinxcontrib-wavedrom
```

`conf.py`に以下の記述を追加してください。

```python
extensions += ['sphinxcontrib.wavedrom']
```

```todo
sphinxcontrib-wavedromにPRを送って、png出力を可能にする
```
