# framework
maya関連における便利スクリプトを公開


_______________________

# BakeFrameTool.py
★アニメーションを任意の間隔でベイクできるツール

＜使い方＞ <br> 
アニメーションキーがあるオブジェクトを選択し、GUIに数字を打ち込んでBakeボタンを押す。 <br> 
5F間隔でアニメーションキーを残したい場合は5と入力すればよい。 <br> 
下の段にはカンマ区切りで数字を打ち込む。（例: 5,12,20）

"Paste the following line into Maya Python." <br> 
mayaの呼び出しコマンド↓
```
import BakeFrameTool <br> 
bake_frame_tool.show()
```
_______________________
