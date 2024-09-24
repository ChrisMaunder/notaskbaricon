# Creating an application with no taskbar icon

A simple method to create a main window that does not appear in the windows taskbar

This is a technique I first saw in Mike Blaszczak's 'stealth' program.

It is desirable sometimes to not have your application window show up in the taskbar. For instance, you may have an application resides in the system tray, and since it already has a system tray icon, having the extra icon in the taskbar is needless duplication. A simple way to create a window that will not have an icon in the taskbar is to create a separate invisible window, and have that invisible window be the parent of your applications window.

The way to do this, and still allow your application's window to remain visible, is to set the invisible window as parent in your application's `PreCreateWindow` override.

First, declare a window member variable in your Main Frame class:

```cpp
class CMainFrame : public CFrameWnd
{
...
protected:
    CWnd m_wndInvisible;
...
```

Then override `CMainFrame::PreCreateWindow`:

```cpp
BOOL CMainFrame::PreCreateWindow(CREATESTRUCT& cs)
{
    if (!CFrameWnd::PreCreateWindow(cs))
         return FALSE;

     // Create invisible window
     if (!::IsWindow(m_wndInvisible.m_hWnd))
     {
        LPCTSTR pstrOwnerClass = AfxRegisterWndClass(0);
        if (!m_wndInvisible.CreateEx(0, pstrOwnerClass, _T(""), WS_POPUP,
                CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT,
                NULL, 0))
            return FALSE;
     }

    cs.hwndParent = m_wndInvisible.m_hWnd;

    return TRUE;
}
```

That's all you need to do! The invisible window will be automatically destroyed when the main application closes.
