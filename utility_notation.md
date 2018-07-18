# Vim
## ファイル名の一括変換
1. 現在のディレクトリ配下のファイルをバッファに展開
2. パイプで出力結果を渡し、(最終行に)発生した空白行を削除
3. 全ての行に対して、"mv ファイル名 ファイル名"の形で出力(後者を修正すれば一括変換可能)
4. Windowsの場合は、`:set fenc=cp932`も加えた方が良い
```
:put! =expand('*') | g/^$/d | %s/\(.*\)$/mv "\1" "\1"/ge | noh
:w !sh    # unix
:w !cmd   # windows
```
## 桁数を揃えた連番を作成  
```
:put! =range(1,100) | g/^$/d | [start],[end]s/^\d\{1}$/0\0/ge | [start],[end]s/^\d\{2}$/0\0/ge
```

## 現在の行を変数に格納して活用
```
:let ln=line('.') | execute ln.",$s/{src}/{dest}/"
:let ln=line('.') | put! =range(1,100) | g/^$/d | execute ln.",$s/^\\d\\{1}$/0\\0/ge" | execute ln.",$s/^\\d\\{2}$/0\\0/ge"
```

## 特定の文字列を含むファイルからディレクトリを作成
```
:put! =expand('\[*\]*') | g/^$/d | %s/\[\(.*\)\].*/mkdir "\1"/ge | sort u | w !sh
```

