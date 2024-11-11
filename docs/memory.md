
Everything About Memory!

----------------------------------------------------------------------------------------------------------------------
1. VIRTUAL MEMORY
----------------------------------------------------------------------------------------------------------------------

"The lowest twelve bits of the virtual address are the offset into the page itself." [https://lwn.net/Articles/717293/]
	* page size = 2^12 = 4096 (~4k)
----------------------------------------------------------------------------------------------------------------------
PAGE TABLES

*Address Translation with a Page Table
	Every process has its own page table.
	Page Table Base Register (PTBR): pointer to the begining of the page table for a specific process
	Modern page tables are arranged as arrays, where every entry is of fixed size. i-th entry in the page table is the i-th virtual page of the memory layout of that process.
	Valid/Present bit: whether or not this mapping is true (page table hit/miss)
	If physical and virtual pages are of the same size, which is the way that makes the most sense, nothing special has to happen to translate VPO to PPO.
	Page fault happens in order to bring things into RAM.

				 ---------------------------------------------------------	
Virtual address: | Virtual Page Number (VPN) | Virtual Page Offset (VPO) |
				 ---------------------------------------------------------
				 	|													|
					| 	------------------------------------------		|
Page table:			--> | valid bit | Physical Page Number (PPN) |		|
					  	------------------------------------------		|
										|								|
										| valid bit = 1					|
										V								V	
					-----------------------------------------------------------
Physical address:	| Physical Page Number (PPN) | Physical Page Offset (PPO) |
					-----------------------------------------------------------

									-----------------------------------------------------------
*Extend PTEs with perission bits:	| VALID/PRES | WRITE | USER | EXEC | Physical Page Number |
									-----------------------------------------------------------
	WRITE: 	1 if wrting to that page is allowed
	USER: 	1 if we are allowed to access it in user mode, 0 if kernel-moode only.
	EXEC: 	1 if executable code is allowed in that page of memory (security defense).
	MMU checks these bits on each access.
	Virtual page number is the offset.

*Types of memory in use by a process:
	user-level text: 	WRITE = 0 (a security vulnerability or too complicated self-modifying code), USER = 1, EXEC = 1
	user-level stack:	WRITE = 1, USER = 1, EXEC = 0 (we don't load code into the stack)
	user-level heap:	WRITE = 1, USER = 1, EXEC = 0 (we don't load code into the heap, eventhough they are in the same basic area of memory)
	kernel text:		WRITE = 0, USER = 0, EXEC = 1
	kernel stack:		WRITE = 1, USER = 0, EXEC = 0
	kernel stack:		WRITE = 1, USER = 0, EXEC = 0

*Speeding up Translation with a Translation Lookaside Buffer (TLB)
	small set-associative hardware cache in MMU - PTE cache on MMU
	mapps virtual page numbers to physical page numbers
	contains complete page table entries for small number of pages
	TLB uses the virtual page number as an index into a set-associative cache to keep track of page table entries.
	A TLB hit eliminates a memory access. In this case, the only time CPU touches main memory (through cache hierarchy) is doing the load/store from/to the physical address.
	A TLB miss incures an additional memory access (the PTE).
	If TLB miss: MMU asks main memory for the page table entry that lives at a specific physical address (we know the base register and we know which virtual page number we need).

*Accessing the TLB
	MMU uses the VPN portion of the virtual address to access the TLB

					<-------------- VPN --------------->
					n-1           p+t p+t-1            p p-1  0
					-------------------------------------------
virtual address:	| TLB tag (TLBT) | TLB index (TLBI) | VPO |
					-------------------------------------------
								|					|
								---------			|
	TLBT matches tag of line within set |			| TLBI selects the set
										|			|
Set 0 									|			|
										V			|
		-----------------		-----------------	|
set 1 	| v | tag | PTE |  ...	| v | tag | PTE |<---
		-----------------		-----------------
...

set T-1 (T = 2^t sets)


*Sizing the page table:
	All modern computers use byte-addressable memory.
	An N-bit architecture does not mean we need to have N-bit physical address size.
	Width of every PTE = meta data + PPN

													-------------------------------------
Logical address (address of an individual byte):	| Page p | Offset i (within a page) |
													-------------------------------------
														|							|
														V 							|
	Number of bits n specifies max size of table, where number of entries = 2^n 	V
																				Number of bits n specifies page size (for example 12 bits for 4096-byte pages)
*Modern X86_64 processors
	48 bit logical (virtual) address: 36 bit page number + 12 bit offset (~4KB pages)
	52 bit physical address --> 52 - 12 = 40 bit physical frame number
	PTE size: 40 bits (frame) + meta data = 64 bits
	2^36 entries
	page table size = 512 GB?! --> solution: Multi Level Page Table (MLPT)


*Translating with a k-level page table
	For the first-level (highest-level) page table, the base address is stored in the page table base address. VPN1 is the offset.
	Each entry in the first-level page table is a physical address that is the base of a second-level page table. VPN2 is the offset of this page table. And so on...
	Some page table entries might be null, in which case there is no entry array for that region of memory.
	On the last level, we find a physical page number (PPN) that does not refer to another page table (i.e. it is not the base of a page table), but it is the actual physical page number.


* Linux Multi-Level Page Tables

				-----------------------------------------------------------------------------------------------------
				|		VPN1		|		VPN2		|		VPN3		|		VPN4		|		VPO			|
				-----------------------------------------------------------------------------------------------------
							|
							|
							|
							|		---------
CR3	------------ /40/ ----------->	|		|
Physical address of L1 PT	|		|		| 
							|		|-------|
							- /9/ ->|L1 PTE |
									|-------|
									---------
