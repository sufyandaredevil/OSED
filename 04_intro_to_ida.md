## MODULE 4: Introduction to IDA Pro

### IDA 101:
- Main Disassembly Window (code showed in 3 different ways)
  - Proximity View
    - For viewing, browsing relationships between functions, global variables, constants. Goto: View > Open subviews > Proximity Browser to open it
  - Text View
    - Here jumps are indicated by the arrows at the left side
  - Graph View
    - Assembly code divided in basics blocks separated by conditional statements or logical splits
      - These basic block can be given separate colors with the help of the color palette icon to the block's top left corner that helps empower our visual understanding of code flow in the graph
    - Control flow represented in form of arrows
        - Green arrow means a jump is taken
        - Red arrows mean a jump is not taken
        - Blue means only a single potential successor block is present through a JMP asm instruction
    - By default virtual addresses are not shown for the code shown here and to enable them: Options > Disassembly Tab > Display disassembly line parts > ENABLE Line prefixes (graph)
- Function Window
  - Provides a list of all the functions present in the program that IDA obtained through automated analysis
  - Double clicking dislays the function's body in the Disassembly Window
  - To jump to a function: Jump > Jump to function > RIGHT_CLICK > Quick filter > SEARCH (same goes for global variables search by using the Jump by name option)
  - Right clicking and Edit function to Rename it
- Imports (same as Function Window)
- Exports (same as Function Window)
- Graph Overview
  - Shows a generic view of where the current disassembly windows is being focused on with dotted lines

### IDA Shortcuts:
- General:
  - <kbd>Space</kbd> - Switch between Graph View and Text View
  - <kbd>:</kbd> - Comment on after clicking on a specific block in a specific line of code in a graph or text view
  - <kbd>N</kbd> - Rename function, global variable after highlighting code with mouse in the disassembly window
  - <kbd>Alt</kbd>+<kbd>M</kbd> - Bookmark a line in disassembly window after a mouse click
  - <kbd>Ctrl</kbd>+<kbd>M</kbd> - View all bookmarks and double click to jump
  - <kbd>X</kbd> - Cross References(list of all who have called that specific function or made use of a global variable) of a specific function or global variable after clicking them in the disassembly window
  - <kbd>G</kbd> - Jump to given address
- Searching:
  - <kbd>Alt</kbd>+<kbd>I</kbd> - Search for immediate value like a DWORD
  - <kbd>Alt</kbd>+<kbd>B</kbd> - Search for specific Byte Sequence

### Static and Dynamic Analysis Synchronization:
- To easily jump back and forth between the WinDBG and IDA, we need to make sure that the base address in both analysis be the same
- When a Windows executable or DLL file is compiled and linked, the PE header, ImageBase field defines the preferred base address when loaded into memory
- Often this will not be the address used at runtime, due to other circumstances such as colliding modules or due to ASLR
- However rebasing can be done in IDA to sync virtual address in both the the tools as follows:
  - In windbg `lm m <module_name>` let us know the start and end VA of the debugged application
  - In IDA goto: Edit > Segments > Rebase program > Value > REPLACE_HERE
