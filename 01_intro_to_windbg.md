## MODULE 1: Introduction to WinDBG

### WinDBG shortcuts:
- **<kbd>F6</kbd>** - Attach to process

---

### WinDBG General commands:
- `.cls` - clear screen
- `g` - run program till IP hits a breakpoint
- `.reload /f` - force reload symbols for the currently loaded modules even if they're considered up to date
- `u` - unassemble running program
  - ex 1: **u** - unassemble from the IP
  - ex 2: **u kernel32!GetCurrentThread** - unassemble the **GetCurrentThread** api from **kernel32**
- `?? sizeof(ntdll!_TEB)` - size of given structure
- `p` - executes single instruction at a time and step over function calls
- `t` - executes single instruction at a time and step into function calls
- `pt` - step to next return or fast forward to end of a function
- `ph` - step until a branching instruction is reached (includes condition, unconditional branches, function calls, return instructions)
- `lm` - display all loaded modules including start and end address in Virtual Memory Space
  > **NOTE**: symbols wouldn't be loaded when the debugged application starts for the first time, so use the `.reload /f` command
- `lm m <module>` - browse module list showing start & end address of a specific module
  - ex 1: `lm m ole32`
- `lm m <partial_module_name>*` - filter all modules start with the partially mentioned module name
  - ex 1: `lm m kernel*`
- `x` - Examine to learn more about the symbols of loaded modules
  - ex 1: `x kernelbase!CreateProc*`

---

### WinDBG Expression evaluation commands:
  - `?` - Evaluate expressions
    - ex 1: `? 77269bc0 - 77231430`
    - ex 2: `? 77269bc0 >> 18`
  - `0n` - convert given decimal to hex
  - `0y` - convert given binary to both decimal, hex
  - `.formats` - converts to all other formats
    - ex 1: `? 41414141` - displays the decimal equivalent of given hex
    - ex 2: `? 0n41414141` - displays the hex equivalent of given decimal
    - ex 3: `? 0y1110100110111` - displays the decimal, hex equivalent of given binary
    - ex 4: `.formats 41414141` - displays the decimal, hex, bin, oct, chars, time, float, double equivalent of given value

---

### WinDBG Display commands:
- Display formats:(default length of displaying data is **80h**, use **L** followed by a number(Ln) to change it)
  - `poi` - display data through pointer to data by reference data from memory address
    - ex 1: **dd poi(esp)** - display contents present in the memory address that is present in the esp
      - this inturn is helpful instead of using **dd esp L1** and then **dd <mem_address_present_in_esp>**
  - `db <registers|address|symbol_name> L<length_n_to_display>` - display bytes
    - ex 1: **db esp** - display content present in given register
    - ex 2: **db 0695fdcc** - display content present in given mem address
    - ex 3: **db kernel32!WriteFile** - display content present in given symbol name
  - `dw <registers|address|symbol_name> L<length_n_to_display>` - display words (group of 2 bytes)
  - `dd <registers|address|symbol_name> L<length_n_to_display>` - display double words (group of 4 bytes)
  - `dq <registers|address|symbol_name> L<length_n_to_display>` - display quad words (group of 8 bytes)
  - For displaying ASCII characters:
    - ex 1: **dc KERNELBASE+0x40** - display memory in byte-sized units
    - ex 2: **dW KERNELBASE** - display memory in word-sized units
  - `dt` -  (display type) command is used to display the members and values of a specified type or structure. This command is particularly useful for examining the contents of data structures in memory during debugging.
    - ex 1: **dt ntdll!_TEB**
    - ex 2: **dt ntdll!_TEB @$teb ThreadLocalStoragePointer** - display specific field for the given address in pseudo register teb
      - ntdll!_TEB: Specifies the type _TEB within the ntdll module
      - _TEB structure is defined in ntdll.dll, which is a core system module in Windows.
    > **NOTE:** you need to have the appropriate symbols and debugging information available to use the **dt** command effectively. Also there are field type that is also a structure and those are prefixed with a `_` symbol followed with CAPITAL letters of the structure  
    > `-r` flag can be added to display any nested structures  
    >   - ex 1: `dt -r ntdll!_TEB @$teb`

---

### WinDBG Write commands:
- `ed` - edit a given register's value or in a specific memory address
  - ex 1: **ed esp 41414141** - placing the value **0x41414141** in the register **esp**
- `ea|eu` - write or edit ascii|unicode characters
  - ex 1: **ea esp "Hello"**

---

### WinDBG Search command:
- `s -<memory_type> <search_start_address> L?<search_end_address> <pattern to search for>`
  - ex 1: **s -d 0 L?80000000 41414141** (searching for the value 41414141)
  - ex 2: **s -a 0 L?80000000 "This program cannot be run in DOS mode"** (searching ascii value)
  - ex 2: **s -u 0 L?80000000 "This program cannot be run in DOS mode"** (searching unicode value)

---

### WinDBG Register inspection and editing commands
- General registers:
  - `r` - display all registers
  - `r <register>` - display a specific register
    - ex 1: **r eax**
  - `r eax=<value>` - change value in a register
    - ex 1: **r ecx=41414141**
- Pseudo registers:
  - These are not used by the CPU but are variables predefined by WinDBG starting from `$t0` to `$19` (20 of them).
  - Always when using a regular or a pseudo regiser always use `@` prefix character cause WinDBG then won't try resolving it as a symbol first
  - ex 1: `r @$t0 = (41414141 - 414141) * 0n10` - evaluate and store the result in the pseudo register **$t0**
  - ex 2: `r @$t0` - display the value stored in pseudo register **$t0**
  - ex 2: `r @$t0 << 8` - evaluating **$t0** with another operand performing left shift and print

---

### WinDBG Breakpoints commands:
- `bd <breakpoint_index>` - disable a breakpoint
- `be <breakpoint_index>` - enable a breakpoint
- `bc <breakpoint_index>` - clear a breakpoint
  - Software Breakpoints:
    - `bp <library!function>` - set a breakpoint at a specific function in the program being debugged
      - ex 1: **bp kernel32!WriteFile**
      > **NOTE:** hit `g` in the console to continue execution till a breakpoint is hit
    - `bl` - list all breakpoints
      > **NOTE:** **breakpoint_index** can be found from the `bl` command (mentioned in the first column)  
      > Also wildcard (`*`) can be used for multiple selection of breakpoint
    - `bu` - set unresolved breakpoint
      - ex 1: `bu ole32!WriteStringStream` - set unresolved breakpoint in the unresolved function that resides in the module **ole32**
      > **NOTE**: The breakpoint will be resolved as soon as the module is loaded in memory but won't get triggered
    - Breakpoint based actions:
      - `bp kernel32!WriteFile ".printf \"The number of bytes written is: %p\", poi(esp + 0x0C);.echo;g"` - set a breakpoint **(bp)** at the **WriteFile** function in the **kernel32** module and when triggered print **(.printf)** a message indicating the number of bytes written **(poi(esp + 0x0C) [cause (esp+0x0C) points to the address of the 3rd parameter(nNumberOfBytesToWrite) present within the offset of esp passed for the WriteFile() function])** along with a new line **(.echo)** and then resume program execution **(g)** (notepad was opened and few bytes were written and saved for this demo)
      - `bp kernel32!WriteFile ".if (poi(esp + 0x0C) != 4) {gc} .else {.printf \"The number of bytes written is 4\";.echo;}"` - set a breakpoint at WriteFile() and if the number of bytes saved is not equal to 4 go from conditional breakpoint to resume execution **(gc)** else print the number of bytes written is 4 followed by a newline
  - Hardware Breakpoints:
    - `ba <r|w|e> <size_in_byte> <memory_address>` - setup a harware breakpoint
      - ex 1: `ba e 1 kernel32!WriteFile` - setup a hardware breakpoint for a byte size of 1 (check whether that specific 1 byte is executed(e)) when the function **WriteFile()** gets executed **(e)** by the processor (typed random string in notepad and search for the specific string in memory and a hbp was set using its memory address to see for any writes in that region)

---

### MISC:
  - Software breakpoint used to replace an instruction with `INT 3` at the location where a breakpoint is set
  - Hardware or Processor breakpoints are stored in the processor's debug registers
  - x86 and x64 architectures uses only 4 debug registers
