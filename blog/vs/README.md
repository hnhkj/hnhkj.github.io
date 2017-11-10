# Visual Studio

## Keys

* IDM_AOUBTBOX - ID-Menu
* IDD_ABOUTBOX - ID-Dialog
* IDC_
* IDR_  - ID Resispone
* IDI_  - ID Icon
* IDD_  - ID Dialog
* IDB_  - ID Bitmap
* IDS_  - ID String Table


* Group Box - 组框
* Radio Button - 单选按钮，在一组中，一次只能选择一个按钮
* Check Box - 复选框， 在一组中，一次可以选择多个复选框

## Visual C++



## Class

* CWnd
    * CFrameWnd
        * CMDIChildWnd
            * user MDI windows
        * CMDIFrameWnd
            * user MDI workspaces
        * CMiniFrameWnd
        * user SDI windows
        * ColeIPFrameWnd
    * CSplitterWnd
    * CControlBar
        * CDialogBar
        * COleResizeBar
        * CRebar
    * CDialog
        * CCommonDialog
            * CColorDialog
            * CFileDialog
            * CFindReplaceDialog
            * CFontDialog
            * COleDialog
                * COleBusyDialog
                * COleChangeIconDialog
                * COleChangeSourceDialog
                * COleCovertDialog
                * COleInsertDialog
                * COleLinkDialog
                    * COleUpdateDialog
    * CView
        * CCtrlView
            * CEditView
            * CListView
            * CRichEditView
            * CTreeView
        * CScrollView
            * user scroll views
            * CFormView
                * user form views
                * CDaoRecordView
                * CHtmlView
                * COleDBRecordView
                * CRecordView

## 对话框控件

#### Dialog

* Dialog
    * IDD_DIALOGBAR
    * IDD_FORMVIEW
    * IDD_OLE_PROPPAGE_LARGE
    * IDD_OLE_PROPPAGE_SMALL
    * IDD_PROPPAGE_LARGE
    * IDD_PROPPAGE_MEDIUM
    * IDD_PROPPAGE_SMALL

#### funciton

* DoModal()
* SetWizardMode()
* SetWizardButtons(DWORD dwFlags)
    * dwFlags
        - PSWIZB_BACK
        - PSWIZB_NEXT
        - PSWIZB_FINISH
        - PSWIZB_DISABLEDFINISH
* OnSetActive()

#### Check Box

* Appearance//外观
    * Bitmap
        - Specifies that the control displays a bitmap instead of text
    * Caption
        - Specifies the text displayed by the control
    * Client Edge
        - Specifies that the control will have a border with a sunken edge
    * Flat
        - Specifies that the visual appearance of the control is two-dimensional.
    * Horizontal Alignment
        - One of: Default, Right, Left, and Center
    * Icon
        - Specifies that the control displays an icon instead of text
    * Left Text
        - Positions the control's text to the left instead of to the right.
    * Modal Frame
        - Specifies that the control will have a double border.
    * Multiline
        - Wraps control text to multiple lines if the text is too long for control width
    * Notify
        - Notifies the parent window if the control has been clicked or double-clicked.
    * Push Like
        - Specifies that the control will look and act like a push button.
    * Right Align Text
        - Specifies that the control's text is right-aligned.
    * Right to Left Reading Order
        - For languages that support reading order alignment, specifies right-to-left reading order.
    * Static Edge
        - specifies that the controol will have a three dimensional border.
    * Transparent
        - Specifies that the control will have a transparent background.
    * Vertical Alignment
        - One of: Default, Bottom, Top, or Center.
* Behavior//行为
    * Accept Files
        - Specifies that the control will accept drag and drop files.
    * Auto
        - Specifies that the check box automatically toggles its state when selected.
    * Disabled
        - Specifies that the control will be initially disabled.
    * Help ID
        - Assigns a help ID based on the resource ID to the control.
    * Tri-state
        - Specifies that the checkbox is tristate.
    * Visible
        - Specifies that the control is iniially visible.
* Dynamic Layout//动态布局
    * Moving Type
        - Defines move direction of a child control when a hosting window is being resized.
    * Sizeing Type
        - Defines how to resize a child control when a hosting window is being resized.
* Misc//杂项
    * (Name)
    * Group
        - Specifies the first control in a group of controls based on tab order.
    * ID
        - Specifies the identifier of the control
    * Tabstop
        - Specifies that the user can move to this control with the TAB key.


## View

#### CheckBox

CheckBox 控件概述（Windows 窗体）

.NET Framework (current version) 其他版本 
 
Windows 窗体 CheckBox 控件指示某个特定条件是处于打开还是关闭状态。 它常用于为用户提供是/否或真/假选项。 可以成组使用复选框 (CheckBox) 控件以显示多重选项，用户可以从中选择一项或多项。
复选框 (CheckBox) 控件和单选按钮 (RadioButton) 控件的相似之处在于，它们都是用于指示用户所选的选项。 它们的不同之处在于，在单选按钮组中一次只能选择一个单选按钮。 但是对于复选框 (CheckBox) 控件，则可以选择任意数量的复选框。
复选框可以使用简单数据绑定连接到数据库中的元素。 多个复选框可以使用 GroupBox 控件进行分组。 这对于可视外观以及用户界面设计很有用，因为成组控件可以在窗体设计器上一起移动。 有关更多信息，请参见Windows 窗体数据绑定和 GroupBox 控件（Windows 窗体）。
CheckBox 控件有两个重要属性：Checked 和 CheckState。 Checked 属性返回 true 或 false。 CheckState 属性返回 Checked 或 Unchecked；如果 ThreeState 属性被设置为 true，则 CheckState 还可能返回 Indeterminate。 处于不确定状态时，该框会显示为灰显外观，指示该选项不可用。

## 参考

#### microsoft

<https://msdn.microsoft.com/zh-cn/library/cc309030.aspx>

<https://docs.microsoft.com/zh-cn/windows/uwp/xaml-platform/xaml-overview>
