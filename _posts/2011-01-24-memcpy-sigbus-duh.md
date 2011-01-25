---
layout: article
uuid: 2AEE9EB6-4EF6-471C-A945-192BAE010E35
title: memcpy SIGBUS (duh)
name: memcpy-sigbus-duh
created_at: 2011-01-24
updated_at: 2011-01-24
categories: thesystemisntdown
---

I just had a `#FFD700` moment.

A quick Google of `memcpy SIGBUS` lead me on a wild goose chase about alignment errors.

However, the problem for me was an extremely simple one - the kind that you might expect `-Wall -Werror` should catch.

    int main(int argc, char* argv[])
    {
      char* str = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ"; // 36 + 1
      memcpy(str, "abcdef", 3);

      printf("%s\n", str);
      // abcdef6789...

      return 0;
    }

What's the issue here? Well, `str` is actually a `const char*` because it's loaded into read-only memory!

    int main(int argc, char* argv[])
    {
      const char* str_const = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ"; // 36 + 1
      char* str = malloc(strlen(str_const) + 1);
      memcpy(str, str_const, strlen(str_const) + 1);
      memcpy(str, "abcdef", 3);

      printf("%s\n", str);
      // abcdef6789...

      return 0;
    }
