# -*- coding: utf-8 -*-
from PySide2 import QtWidgets, QtCore
import maya.cmds as cmds
import shiboken2
import maya.OpenMayaUI as omui

def get_maya_main_window():
    # Mayaのメインウィンドウを取得する関数。
    main_window_ptr = omui.MQtUtil.mainWindow()
    return shiboken2.wrapInstance(int(main_window_ptr), QtWidgets.QWidget)

class Bake_Frame_Tool(QtWidgets.QDialog):
    def __init__(self, parent=None):
        # Bake_Frame_Toolクラスの初期化。
        super(Bake_Frame_Tool, self).__init__(parent or get_maya_main_window())
        self.setWindowTitle("Bake_Frame_Tool")
        self.setFixedSize(300, 350)  # サイズを大きくしました。

        self.create_ui()

    def create_ui(self):
        # ツールのUIを作成する関数。
        layout = QtWidgets.QVBoxLayout(self)

        title_label = QtWidgets.QLabel("Bake_Frame_Tool", self)
        title_label.setAlignment(QtCore.Qt.AlignCenter)
        layout.addWidget(title_label)

        # フレーム間隔を入力するスピンボックス
        self.frame_interval_spinbox = QtWidgets.QSpinBox(self)
        self.frame_interval_spinbox.setMinimum(1)
        self.frame_interval_spinbox.setValue(5)
        layout.addWidget(QtWidgets.QLabel("Frame Interval:", self))
        layout.addWidget(self.frame_interval_spinbox)

        # 残すフレームを入力するラインエディット
        self.keep_frames_lineedit = QtWidgets.QLineEdit(self)
        layout.addWidget(QtWidgets.QLabel("Keep Frames (comma separated, 10,30,40):", self))
        layout.addWidget(self.keep_frames_lineedit)

        # アトリビュート選択のグループボックス
        self.create_attribute_selection(layout)

        self.bake_button = QtWidgets.QPushButton("Bake", self)
        self.bake_button.clicked.connect(self.bake_framer)
        layout.addWidget(self.bake_button)

    def create_attribute_selection(self, layout):
        # アトリビュート選択のグループボックスを作成する関数。
        self.translate_group = QtWidgets.QGroupBox("Translate", self)
        self.rotate_group = QtWidgets.QGroupBox("Rotate", self)
        self.scale_group = QtWidgets.QGroupBox("Scale", self)

        translate_layout = QtWidgets.QHBoxLayout(self.translate_group)
        rotate_layout = QtWidgets.QHBoxLayout(self.rotate_group)
        scale_layout = QtWidgets.QHBoxLayout(self.scale_group)

        self.translate_all = QtWidgets.QCheckBox("all", self.translate_group)
        self.translate_x = QtWidgets.QCheckBox("x", self.translate_group)
        self.translate_y = QtWidgets.QCheckBox("y", self.translate_group)
        self.translate_z = QtWidgets.QCheckBox("z", self.translate_group)
        self.translate_all.setChecked(True)  # 初期状態をすべてチェック

        self.rotate_all = QtWidgets.QCheckBox("all", self.rotate_group)
        self.rotate_x = QtWidgets.QCheckBox("x", self.rotate_group)
        self.rotate_y = QtWidgets.QCheckBox("y", self.rotate_group)
        self.rotate_z = QtWidgets.QCheckBox("z", self.rotate_group)
        self.rotate_all.setChecked(True)  # 初期状態をすべてチェック

        self.scale_all = QtWidgets.QCheckBox("all", self.scale_group)
        self.scale_x = QtWidgets.QCheckBox("x", self.scale_group)
        self.scale_y = QtWidgets.QCheckBox("y", self.scale_group)
        self.scale_z = QtWidgets.QCheckBox("z", self.scale_group)

        translate_layout.addWidget(self.translate_all)
        translate_layout.addWidget(self.translate_x)
        translate_layout.addWidget(self.translate_y)
        translate_layout.addWidget(self.translate_z)

        rotate_layout.addWidget(self.rotate_all)
        rotate_layout.addWidget(self.rotate_x)
        rotate_layout.addWidget(self.rotate_y)
        rotate_layout.addWidget(self.rotate_z)

        scale_layout.addWidget(self.scale_all)
        scale_layout.addWidget(self.scale_x)
        scale_layout.addWidget(self.scale_y)
        scale_layout.addWidget(self.scale_z)

        layout.addWidget(self.translate_group)
        layout.addWidget(self.rotate_group)
        layout.addWidget(self.scale_group)

    def bake_framer(self):
        # Bake処理を実行する関数。
        frame_interval = self.frame_interval_spinbox.value()
        keep_frames = self.keep_frames_lineedit.text()
        attributes = self.get_selected_attributes()
        self.bake_frames(frame_interval, keep_frames, attributes)

    def get_selected_attributes(self):
        # 選択されたアトリビュートを取得する関数。
        attributes = []
        if self.translate_all.isChecked():
            attributes.extend(["tx", "ty", "tz"])
        else:
            if self.translate_x.isChecked():
                attributes.append("tx")
            if self.translate_y.isChecked():
                attributes.append("ty")
            if self.translate_z.isChecked():
                attributes.append("tz")

        if self.rotate_all.isChecked():
            attributes.extend(["rx", "ry", "rz"])
        else:
            if self.rotate_x.isChecked():
                attributes.append("rx")
            if self.rotate_y.isChecked():
                attributes.append("ry")
            if self.rotate_z.isChecked():
                attributes.append("rz")

        if self.scale_all.isChecked():
            attributes.extend(["sx", "sy", "sz"])
        else:
            if self.scale_x.isChecked():
                attributes.append("sx")
            if self.scale_y.isChecked():
                attributes.append("sy")
            if self.scale_z.isChecked():
                attributes.append("sz")
        return attributes

    def bake_frames(self, frame_interval, keep_frames, attributes):
        # 実際のベイク処理を行う関数。
        selected_objs = cmds.ls(selection=True)

        if not selected_objs:
            cmds.warning("There are no selected objects")
            return

        st = int(cmds.playbackOptions(min=1, q=1))
        ed = int(cmds.playbackOptions(max=1, q=1))

        # undoをまとめるためにundoInfoを使用
        cmds.undoInfo(openChunk=True)
        try:
            cmds.ogs(pause=True)
            cmds.bakeResults(selected_objs, sm=True, t=(st, ed), at=attributes)  # 選択されたアトリビュートのみベイク
            cmds.ogs(pause=True)

            frame_list = [ed, ]
            for i in range(st, ed + 1):
                if i % frame_interval == 0:
                    frame_list.append(i)

            if keep_frames:
                keep_frames = keep_frames.split(',')
                for frame in keep_frames:
                    frame = frame.strip()
                    if frame.isdigit():
                        frame_list.append(int(frame))

            # ベイクされたアトリビュートのみcutKeyを実行
            for obj in selected_objs:
                keys = cmds.keyframe(obj, q=True, at=attributes)
                if keys:
                    for key in keys:
                        if key not in frame_list:
                            cmds.cutKey(obj, time=(key, key), at=attributes)  # 選択されたアトリビュートのみcutKey
        finally:
            cmds.undoInfo(closeChunk=True)  # undoを閉じる

# ツールを表示
if __name__ == "__main__":
    try:
        bake_frame_tool.close()
        bake_frame_tool.deleteLater()
    except:
        pass

    bake_frame_tool = Bake_Frame_Tool()
