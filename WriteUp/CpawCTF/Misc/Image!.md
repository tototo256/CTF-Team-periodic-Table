## Q19.[Misc]Image!
### 問題文
```
Find the flag in this zip file.
```
### 添付ファイル
`misc100.zip`
### 解き方
1. 展開して中のxmlファイルを見ると，office:document-contentとあるのでoffice系ファイルであることが分かる
2. mimetypeを見るとapplication/vnd.oasis.opendocument.graphとある．
3. よってodgファイル形式であることが分かるので，misc100.zipをmisc100.odgに変えるとLibraOffice Drawで開ける．
4. Wordで開けたので開くとフラグが書いてある
5. フラグ cpaw{It_is_fun__isn't_it?}