I guess I could've used the `libjansson-dev` package and called it a day. But the whole point of this endeavor is to learn, specifically about dynamic libraries (and, later on, about static libraries, but that will probably be a separate repo).

This small repo is mostly for documentation purposes of things I've learned. Most of my learning revolved around writing a proper Makefile. My memory is not perfect, so this README is dedicated to my future self.

# Useful Phony for debugging
Aside from the classic, `clean` phony target, I also tried `print` phony target. Not ideal debugging mechanism, but it is a start. It helped me check the rules for my targets, in case I messed up my prerequisites.

# gcc
For the `$(OUTPUT_BINARY)`, I decided to compile and link in one command, without the intermediary object file target, which is fine in my case, because I only have one source file.

For multiple source files, I would've created a separate object file rule, so that only the changed files would have to be recompiled, while others would only need to be relinked.

# Order-only prerequisites
I decided to store the generated object files for libjansson.so in a separate folder, called objdir. This folder, defined as `$(OBJDIR)` is marked as order-only prerequisite, meaning it gets created before the target, `$(FINAL_LIB_OBJS)` in this case. However, unlike for normal prerequisites, in case of order-only prerequisites, target is not forced to update if the prerequisite is updated. Thus, if objdir's timestamp changes, `$(FINAL_LIB_OBJS)` won't be forced to rebuilt.
- See: https://www.gnu.org/software/make/manual/html_node/Prerequisite-Types.html for more details on order-only prerequisites (+ source of inspo for `$(OBJDIR)`)


# Ways of letting your compiler know where your dynamic library is at
There are a number of ways to let your loader know where your share lib is at.

First is the classic, just put it into the known lib directory, e.g. `/usr/lib64`, and then `ldconfig`. The `ldconfig` command creates the required links and updates the cache (for shared libs).

Second way can be seen in this repo's `wl_rpath`  branch's version of Makefile. The rule for `$(OUTPUT_BINARY)` contains `$(EXTRA_LFLAGS)` variable that is defined in terms of `-Wl,-rpath` flags. These flags supply the runtime library path.

The third way, in this repo's `ld_library_path` branch's Makefile, modifies the LD_LIBRARY_PATH environmental variable and exports it.



# Sources
- https://cylab.be/blog/234/creating-a-dynamic-library-in-c
- [Letting your ld know of your so's location using known directories](https://opensource.com/article/22/5/dynamic-linking-modular-libraries-linux)
- https://opensource.com/article/22/5/compile-code-ldlibrarypath
- https://gcc.gnu.org/legacy-ml/gcc-help/2005-12/msg00017.html


