There we have 3 fundamental containers for files:
    - buffer
    - window
    - tab

Buffer [hold files]
    The special place in memory where the file is loaded

    :bnext
    :brev
    :bdelete
    :b <number>
    :b <fuzzy_name>


Window (pane) [hold buffers]
    The place where the specified buffer displays

    :split
    :vsplit
    :q
    <C-w>{h,j,k,l}


Tab [hold widows]
    The sectiond where we can place windows

    :tabnew
    :tabe <file_name>
    :tabnext (gt)
    :tabprev (gT)
    :tabclose

