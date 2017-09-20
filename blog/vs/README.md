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