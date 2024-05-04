<!-- 
README.md is a generated file! 

To make modifications, make sure you're editing `templates/README.template.md`.
Then generate the README with `python scripts/generate_readme.py`
-->


<h1 align="center">
  Qtile Bonsai
</h1>

<p align="center">
  <a href="https://github.com/aravinda0/qtile-bonsai/actions?query=branch%3Amaster"><img src="https://img.shields.io/github/actions/workflow/status/aravinda0/qtile-bonsai/ci.yml?style=for-the-badge&logo=github"></a>
  <a href="https://codecov.io/gh/aravinda0/qtile-bonsai"><img src="https://img.shields.io/codecov/c/github/aravinda0/qtile-bonsai?style=for-the-badge&logo=codecov"></a>
  <a href="https://github.com/aravinda0/qtile-bonsai/blob/master/LICENSE.txt"><img src="https://img.shields.io/github/license/aravinda0/qtile-bonsai?style=for-the-badge"></a>
</p>
<p align="center">
  <a href="https://aravinda0.github.io/qtile-bonsai/static/visual_guide/"><img src="https://img.shields.io/badge/Visual%20Guide-d79921?style=flat-square"></a>
  <a href="#reference"><img src="https://img.shields.io/badge/Reference-d79921?style=flat-square"></a>
</p>

<br/>

## Introduction

_Qtile Bonsai_ provides a flexible layout for the
[qtile](https://github.com/qtile/qtile) tiling window manager that allows you to
arrange windows as tabs, splits and even subtabs inside splits.

For a quick feeler, take a look at the demo video below, or the [visual guide](#visual-guide) further below.

<br>

<!-- GitHub will simply render this into a video it seems. Generated by dragging
and dropping into GitHub README editor GUI -->
https://github.com/aravinda0/qtile-bonsai/assets/960763/0e77b61e-1830-4972-9098-516d111b942b


<br/>

## Getting Started

### Installation

Assuming you already have
[qtile up and running](https://docs.qtile.org/en/latest/manual/install/), you
can just install qtile-bonsai from PyPI.

```bash
pip install qtile-bonsai
```

### Configuration

#### 1. Make Bonsai available as a layout in your qtile config

```python
from qtile_bonsai import Bonsai


layouts = [
    Bonsai(**{
      # Specify any desired options here. These examples are defaults.
      "window.border_size": 1,
      "tab_bar.height": 20,
      
      # You can specify subtab level specific options if desired by prefixing
      # the option key with the appropriate level, eg. L1, L2, L3 etc.
      # For example, the following options affect only 2nd level subtabs and
      # their windows. eg:
      # "L2.window.border_color": "#ff0000",
      # "L2.window.margin": 5,
    }),
]
```

#### 2. Add your personal keybindings to your qtile config

```python
from libqtile.config import EzKey, KeyChord
from libqtile.utils import guess_terminal


terminal = guess_terminal()
rofi_run_cmd = "rofi -show drun -m -1"

keys = [
    # Open your terminal emulator quickly. See further below for more bindings
    # to directly open apps as splits/tabs using something like rofi.
    EzKey("M-v", lazy.layout.spawn_split(terminal, "x")),
    EzKey("M-x", lazy.layout.spawn_split(terminal, "y")),
    EzKey("M-t", lazy.layout.spawn_tab(terminal)),
    EzKey("M-S-t", lazy.layout.spawn_tab(terminal, new_level=True)),

    # Motions to move focus. The names are compatible with built-in layouts.
    EzKey("M-h", lazy.layout.left()),
    EzKey("M-l", lazy.layout.right()),
    EzKey("M-k", lazy.layout.up()),
    EzKey("M-j", lazy.layout.down()),
    EzKey("A-d", lazy.layout.prev_tab()),
    EzKey("A-f", lazy.layout.next_tab()),

    # Resize operations
    EzKey("M-C-h", lazy.layout.resize("left", 100)),
    EzKey("M-C-l", lazy.layout.resize("right", 100)),
    EzKey("M-C-k", lazy.layout.resize("up", 100)),
    EzKey("M-C-j", lazy.layout.resize("down", 100)),

    # Swap windows/tabs with neighbors
    EzKey("M-S-h", lazy.layout.swap("left")),
    EzKey("M-S-l", lazy.layout.swap("right")),
    EzKey("M-S-k", lazy.layout.swap("up")),
    EzKey("M-S-j", lazy.layout.swap("down")),
    EzKey("A-S-d", lazy.layout.swap_tab_prev()),
    EzKey("A-S-f", lazy.layout.swap_tab_next()),

    # It's kinda nice to have more advanced window management commands under a
    # qtile key chord.
    KeyChord(
        ["mod4"],
        "w",
        [
            # Use something like rofi to pick GUI apps to open as splits/tabs.
            EzKey("v", lazy.layout.spawn_split(rofi_run_cmd, "x")),
            EzKey("x", lazy.layout.spawn_split(rofi_run_cmd, "y")),
            EzKey("t", lazy.layout.spawn_tab(rofi_run_cmd)),
            EzKey("S-t", lazy.layout.spawn_tab(rofi_run_cmd, new_level=True)),
            
            EzKey("r", lazy.layout.rename_tab()),
            
            EzKey("o", lazy.layout.pull_out()),
            EzKey("u", lazy.layout.pull_out_to_tab()),
            
            # Directional commands to merge windows with their neighbor into subtabs.
            KeyChord(
                [],
                "m",
                [
                    EzKey("h", lazy.layout.merge_to_subtab("left")),
                    EzKey("l", lazy.layout.merge_to_subtab("right")),
                    EzKey("j", lazy.layout.merge_to_subtab("down")),
                    EzKey("k", lazy.layout.merge_to_subtab("up")),

                    # Merge entire tabs with each other as splits
                    EzKey("S-h", lazy.layout.merge_tabs("previous")),
                    EzKey("S-l", lazy.layout.merge_tabs("next")),
                ],
            ),
            
            # Directional commands for push_in() to move window inside neighbor space.
            KeyChord(
                [],
                "i",
                [
                    EzKey("j", lazy.layout.push_in("down")),
                    EzKey("k", lazy.layout.push_in("up")),
                    EzKey("h", lazy.layout.push_in("left")),
                    EzKey("l", lazy.layout.push_in("right")),
                    
                    # It's nice to be able to push directly into the deepest
                    # neighbor node when desired. The default bindings above
                    # will have us push into the largest neighbor container.
                    EzKey(
                        "S-j",
                        lazy.layout.push_in("down", dest_selection="mru_deepest"),
                    ),
                    EzKey(
                        "S-k",
                        lazy.layout.push_in("up", dest_selection="mru_deepest"),
                    ),
                    EzKey(
                        "S-h",
                        lazy.layout.push_in("left", dest_selection="mru_deepest"),
                    ),
                    EzKey(
                        "S-l",
                        lazy.layout.push_in("right", dest_selection="mru_deepest"),
                    ),
                ],
            ),
        ]
    ),
    
    # Your other bindings
    # ...
]
```


## Visual Guide

Click on the image to open a full-size web view.

<a href="https://aravinda0.github.io/qtile-bonsai/static/visual_guide/">![Visual Guide](static/visual_guide/visual_guide.png)</a>


## Reference

### Configuration

> [!TIP]
> Most options have subtab-level support! ie. you can have one setting for top
> level windows (eg. `"window.margin" = 10`) and another setting for windows
> under 2nd level subtabs (eg. `"L2.window.margin" = 5`). Just prefix the option
> with `L<subtab-level>.`

<br>

| Option Name | Default Value | Description |
| ---         | ---           | ---         |
|`window.margin` | 0 | Size of the margin space around windows.<br>Can be an int or a list of ints in [top, right, bottom, left] ordering. |
|`window.border_size` | 1 | Width of the border around windows. Must be a single integer value since that's<br>what qtile allows for window borders. |
|`window.border_color` | #6d531f | Color of the border around windows |
|`window.active.border_color` | #d79921 | Color of the border around an active window |
|`window.normalize_on_remove` | True | Whether or not to normalize the remaining windows after a window is removed.<br>If `True`, the remaining sibling windows will all become of equal size.<br>If `False`, the next (right/down) window will take up the free space. |
|`window.default_add_mode` | tab | (Experimental) Determines how windows get added if they are not explicitly<br>spawned as a split or a tab.<br>Can be one of "tab" or "match_previous".<br>If "match_previous", then then new window will get added in the same way the<br>previous window was. eg. if the previous window was added as a y-split, so will<br>the new window.<br><br>NOTE:<br>Setting this to "tab" may seem convenient, since externally spawned GUI apps get<br>added as background tabs instead of messing up the current split layout.<br>But due to how the window creation flow happens, when many splits are requested<br>in quick succession, this may cause some windows requested as a split to open up<br>as a tab instead. |
|`tab_bar.height` | 20 | Height of tab bars |
|`tab_bar.hide_when` | single_tab | When to hide the tab bar. Allowed values are 'never', 'always', 'single_tab'.<br><br>When 'single_tab' is configured, the bar is not shown whenever there is a lone<br>tab remaining, but shows up again when another tab is added.<br><br>For nested tab levels, configuring 'always' or 'single_tab' actually means that<br>when only a single tab remains, its contents get 'merged' upwards, eliminating<br>the sub-tab level. |
|`tab_bar.margin` | 0 | Size of the margin space around tab bars.<br><br>Can be an int or a list of ints in [top, right, bottom, left] ordering. |
|`tab_bar.border_size` | 0 | Size of the border around tab bars |
|`tab_bar.border_color` | #d79921 | Color of border around tab bars |
|`tab_bar.bg_color` | #282828 | Background color of tab bars, beind their tabs |
|`tab_bar.tab.min_width` | 50 | Minimum width of a tab on a tab bar |
|`tab_bar.tab.margin` | 0 | Size of the margin space around individual tabs |
|`tab_bar.tab.padding` | 20 | Size of the padding space inside individual tabs |
|`tab_bar.tab.bg_color` | #3c3836 | Background color of individual tabs |
|`tab_bar.tab.fg_color` | #ebdbb2 | Foreground text color of individual tabs |
|`tab_bar.tab.font_family` | Mono | Font family to use for tab titles |
|`tab_bar.tab.font_size` | 15 | Font size to use for tab titles |
|`tab_bar.tab.active.bg_color` | #7c6f64 | Background color of active tabs |
|`tab_bar.tab.active.fg_color` | #ebdbb2 | Foreground text color of the active tab |
|`auto_cwd_for_terminals` | True | (Experimental) If `True`, when spawning new windows by specifying a `program`<br>that happens to be a well-known terminal emulator, will try to open the new<br>terminal window in same working directory as the last focused window. |
|`restore.threshold_seconds` | 4 | You likely don't need to tweak this.<br>Controls the time within which a persisted state file is considered to be from a<br>recent qtile config-reload/restart event. If the persisted file is this many<br>seconds old, we restore our window tree from it. |



### Commands

| Command Name | Description |
| ---          | ---         |
|`spawn_split` | Launch the provided `program` into a new window that splits the currently focused window along the<br>specified `axis`.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`program`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The program to launch.<br>&nbsp;&nbsp;&nbsp;&nbsp;`axis`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The axis along which to split the currently focused window. Can be 'x' or 'y'.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;An `x` split will end up with two top/bottom windows.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;A `y` split will end up with two left/right windows.<br>&nbsp;&nbsp;&nbsp;&nbsp;`ratio`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The ratio of sizes by which to split the current window.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If a window has a width of 100, then splitting on the y-axis with a ratio = 0.3 will result<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;in a left window of width 30 and a right window of width 70.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Defaults to 0.5.<br>&nbsp;&nbsp;&nbsp;&nbsp;`normalize`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, overrides `ratio` and leads to the new window and all sibling windows becoming of<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;equal size along the corresponding split axis.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Defaults to `True`.<br>&nbsp;&nbsp;&nbsp;&nbsp;`position`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Whether the new split content appears after or before the currently focused window.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Can be `"next"` or `"previous"`. Defaults to `"next"`.<br><br>Examples:<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.spawn_split(my_terminal, "x")`<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.spawn_split(my_terminal, "y", ratio=0.2, normalize=False)`<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.spawn_split(my_terminal, "x", position="previous")` |
|`spawn_tab` | Launch the provided `program` into a new window as a new tab.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`program`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The program to launch.<br>&nbsp;&nbsp;&nbsp;&nbsp;`new_level`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, create a new sub-tab level with 2 tabs. The first sub-tab being the currently<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;focused window, the second sub-tab being the newly launched program.<br>&nbsp;&nbsp;&nbsp;&nbsp;`level`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If provided, launch the new window as a tab at the provided `level` of tabs in the currently<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;focused window's tab hierarchy.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Level 1 is the topmost level.<br><br>Examples:<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.spawn_tab(my_terminal)`<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.spawn_tab(my_terminal, new_level=True)`<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.spawn_tab("qutebrowser", level=1)` |
|`move_focus` | Move focus to the window in the specified direction relative to the currently focused window. If<br>there are multiple candidates, the most recently focused of them will be chosen.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`wrap`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, will wrap around the edge and select windows from the other end of the screen.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Defaults to `True`. |
|`left` | Same as `move_focus("left")`. For compatibility with API of other built-in layouts. |
|`right` | Same as `move_focus("right")`. For compatibility with API of other built-in layouts. |
|`up` | Same as `move_focus("up")`. For compatibility with API of other built-in layouts. |
|`down` | Same as `move_focus("down")`. For compatibility with API of other built-in layouts. |
|`next_tab` | Switch focus to the next tab. The window that was previously active there will be focused.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`wrap`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, will cycle back to the fist tab if invoked on the last tab.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Defaults to `True`. |
|`prev_tab` | Same as `next_tab()` but switches focus to the previous tab. |
|`resize` | Resizes by moving an appropriate border leftwards. Usually this is the right/bottom border, but for<br>the 'last' node under a SplitContainer, it will be the left/top border.<br><br>Basically the way tmux does resizing.<br><br>If there are multiple nested windows under the area being resized, those windows are resized<br>proportionally.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`amount`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The amount by which to resize.<br><br>Examples:<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.resize("left", 100)`<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.resize("right", 100)` |
|`swap` | Swaps the currently focused window with the nearest window in the specified direction. If there are<br>multiple candidates to pick from, then the most recently focused one is chosen.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`wrap`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, will wrap around the edge and select windows from the other end of the screen to<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;swap.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Defaults to `False`. |
|`swap_tabs` | Swaps the currently active tab with the previous tab.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`wrap`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, will wrap around the edge of the tab bar and swap with the last tab.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Defaults to `True`. |
|`rename_tab` | Rename the currently active tab.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`widget`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The qtile widget that should be used for obtaining user input for the renaming. The 'prompt'<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;widget is used by default. |
|`merge_tabs` | Merge the currently active tab with another tab, such that both tabs' contents now appear in 2<br>splits.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`direction`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Which neighbor tab to merge with. Can be either "next" or "previous".<br>&nbsp;&nbsp;&nbsp;&nbsp;`axis`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The axis along which the merged content should appear as splits.<br><br>Examples:<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.merge_tabs("previous")`<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.merge_tabs("next", "y")` |
|`merge_to_subtab` | Merge the currently focused window (or an ancestor node) with a neighboring node in the specified<br>`direction`, so that they both come under a (possibly new) subtab.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`direction`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The direction in which to find a neighbor to merge with.<br>&nbsp;&nbsp;&nbsp;&nbsp;`src_selection`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Determines how the source window/node should be resolved. ie. do we pick just the current<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;window, or all windows under an appropriate ancestor container.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Valid values are defined in `NodeHierarchySelectionMode`. See below.<br>&nbsp;&nbsp;&nbsp;&nbsp;`dest_selection`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Determines how the neighboring node should be resolved, similar to how `src_selection` is<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;resolved.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Valid values are defined in `NodeHierarchySelectionMode`. See below.<br>&nbsp;&nbsp;&nbsp;&nbsp;`normalize`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, any removals during the merge process will ensure all sibling nodes are resized<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;to be of equal dimensions.<br><br>Valid values for `NodeHierarchySelectionMode` are:<br>&nbsp;&nbsp;&nbsp;&nbsp;`"mru_deepest"`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Pick a single innermost window. If there are multiple such neighboring windows, pick the<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;most recently used (MRU) one.<br>&nbsp;&nbsp;&nbsp;&nbsp;`"mru_subtab_else_deepest"` (default):<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If the target is under a subtab, pick the subtab. If there is no subtab in play, behaves<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;like `mru_deepest`.<br>&nbsp;&nbsp;&nbsp;&nbsp;`"mru_largest"`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Given a window, pick the largest ancestor node that the window's border is a fragment of.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;This resolves to a SplitContainer or a TabContainer.<br>&nbsp;&nbsp;&nbsp;&nbsp;`"mru_subtab_else_largest"`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If the target is under a subtab, pick the subtab. If there is no subtab in play, behaves<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;like `mru_largest`.<br><br>Examples:<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.merge_to_subtab("right", dest_selection="mru_subtab_else_deepest")`<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.merge_to_subtab("up", src_selection_mo="mru_deepest", dest_selection="mru_deepest")` |
|`push_in` | Move the currently focused window (or a related node in its hierarchy) into a neighboring window's<br>container.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`direction`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The direction in which to find a neighbor whose container we push into.<br>&nbsp;&nbsp;&nbsp;&nbsp;`src_selection`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(See docs in `merge_to_subtab()`) `dest_selection`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(See docs in `merge_to_subtab()`) `normalize`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, any removals during the process will ensure all sibling nodes are resized to be<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;of equal dimensions.<br>&nbsp;&nbsp;&nbsp;&nbsp;`wrap`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, will wrap around the edge of the screen and push into the container on the other<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;end.<br><br>Examples:<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.push_in("right", dest_selection="mru_deepest")`<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.push_in("down", dest_selection="mru_largest", wrap=False)` |
|`pull_out` | Move the currently focused window out from its SplitContainer into an ancestor SplitContainer at a<br>higher level. It effectively moves a window 'outwards'.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`position`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Whether the pulled out node appears before or after its original container node.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Can be `"next"` or `"previous"`. Defaults to `"previous"`.<br>&nbsp;&nbsp;&nbsp;&nbsp;`src_selection`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Can either be `"mru_deepest"` (default) or `"mru_subtab_else_deepest"`.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(See docs in `merge_to_subtab()`) `normalize`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, all sibling nodes involved in the rearrangement are resized to be of equal<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dimensions.<br><br>Examples:<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.pull_out()`<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.pull_out(src_selection="mru_subtab_else_deepest")`<br>&nbsp;&nbsp;&nbsp;&nbsp;- `layout.pull_out(position="next")` |
|`pull_out_to_tab` | Extract the currently focused window into a new tab at the nearest TabContainer.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`normalize`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, any removals during the process will ensure all sibling nodes are resized to be<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;of equal dimensions. |
|`normalize` | Starting from the focused window's SplitContainer, make all windows in the container of equal size.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`recurse`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, then nested nodes are also normalized similarly. |
|`normalize_tab` | Starting from the focused window's tab, make all windows in the tab of equal size.<br><br>Args:<br>&nbsp;&nbsp;&nbsp;&nbsp;`recurse`:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If `True`, then nested nodes are also normalized similarly.<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Defaults to `True`. |
|`normalize_all` | Make all windows under all tabs be of equal size. |



## Roadmap

- Basic mouse support (click on tab to focus)
- Grow/shrink style resizing
- Some nicer ricing possibilities for subtabs 


## Support

For any bug reports, please file an issue. For questions/discussions, use the
[GitHub Discussions](https://github.com/aravinda0/qtile-bonsai/discussions)
section, or you can ask on the [qtile subreddit](https://www.reddit.com/r/qtile/).