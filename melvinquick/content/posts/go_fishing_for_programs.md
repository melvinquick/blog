---
date: "2025-05-29T10:50:37-04:00"
draft: false
title: "Go Fishing For Programs"
tags: ["linux", "fish shell", "scripting", "arch"]
categories: ["linux"]
---

## Intro

A common pain point for me is searching my installed programs on my system, or finding programs by searching the repositories. This isn't because running a search is particularly hard, but because sometimes my searches yield a lot of results, which can cause way too much information to be present. To combat this, I decided I wanted to make some simple [Fish Shell](https://fishshell.com/) (my shell of choice) Functions that would allow me to search any keyword I want to, and get ONLY the source repo, the program name, and the program version. This is really useful when you know which packages you want but aren't sure of EXACTLY what the package name is, or just for seeing what's installed on your system under a certain keyword.

## Creating The Functions

We need two functions. They will do basically the same things; however, one will search installed programs and the other will search repository programs.

### Searching Repository Programs

Let's start with searching the repositories. The first thing we need to do was create a function file. Please see [this](#fish-configs) note about the storage of Fish's config and function files. We need our function to do a few things.

1. We need it to allow us to searwch the repositories via keyword.
2. We need it to strip out anything that's not the repository name, the program name, or the program version.
   1. To strip out the extra info, we can just grep for the different repositories since they'll be on the line we need.
3. It needs to be able to throw a message if the user forgets to enter a keyword to be searched on.
   1. This one isn't super important for using this locally yourself, but it's good practice, and since I have my dotfiles hosted online [here](https://codeberg.org/melvinquick/dotfiles), I need to add this so other people that may use this don't have issues.

Our function should look like this:

```
function search_repo_programs -a program_keyword -d "Pass a keyword to find any programs containing that keyword in the repositories"
    if test -z "$program_keyword"
        echo "You need to provide a keyword to search for! e.g., search_repo_programs kubuntu"
    else
        pacman -Ss $program_keyword | grep -e core/ -e extra/ -e community/ -e multilib/ -e testing/ -e staging/ -e aur/ -e local/
    end
end
```

If you save and open your terminal into a Fish Shell, you should now be able to run `search_repo_programs program_name` and get your search results back! For example:

![srp_optiimage_example](/images/go_fishing_for_programs/srp_optiimage_example.png)

You can see that we were returned one result that contained the keyword optiimage! Let's try a different search. This time we'll search for kde as our keyword (this should produce more results), and we're going to pipe that output into the word count program with the line flag turned on, like this `wc -l`:

![repo_search_output_line_count](/images/go_fishing_for_programs/repo_search_output_line_count.png)

Looking at this output, searching using our function produces half (and sometimes even less than half) the amount of line output, so you don't have nearly as much to scroll through! Like I said at the beginning, this is really useful for when you know the package you're looking for, but can't remember exactly the package name. Or when you just want to see packages available under a keyword but don't necessarily need to know what they do.

### Searching Installed Programs

This function needs to do much the same as the previous function we created. The main difference here is that instead of searching the repositories, we just need to search our locally installed programs. To do this we only really need to change our `pacman -Ss` section of our function to instead be `pacman -Qs`. Here's the updated function:

```
function search_installed_programs -a program_keyword -d "Pass a keyword to find any programs containing that keyword installed on your system"
    if test -z "$program_keyword"
        echo "You need to provide a keyword to search for! e.g., search_installed_programs kubuntu"
    else
        pacman -Qs $program_keyword | grep -e core/ -e extra/ -e community/ -e multilib/ -e testing/ -e staging/ -e aur/ -e local/
    end
end
```

If you save the function and re-open your terminal into a Fish Shell, you should see be able to run a search like this:

![sip_optiimage_example](/images/go_fishing_for_programs/sip_optiimage_example.png)

Notice this looks basically identical to our search_repo_programs output, the main difference being that the repository name is now `local/` instead of `extra/`. This is just because all installed packages will show local since they are local to the machine and not remotely in some repo using this search.

## Conclusion

As you can tell, taking advantage of Fish's scripting ability and pacman being an excellent package manager, we can easily produce output that is cleaner and easier to look at for those instances when we don't need super in depth information!

## Bonus Section!

As a little bonus, we can easily make a function to tell us how many packages we have installed on our system in total using methods similar to the above functions. If we use the `pacman -Q` command with no other flags, and then pipe that into the word count program mentioned above, we can make this function:

```
function package_count -d "Gives you a total system package count (via pacman)"
    pacman -Q | wc -l
end
```

Saving, re-opening your terminal, and running the function produces output like this:

![package_count_example](/images/go_fishing_for_programs/package_count_example.png)

Which lets us know we currently have, in this case, 941 packages installed on this system. Pretty handy!

## Notes

### Fish Configs

Fish Configs are stored in `~/.config/fish/` with the main config file being `config.fish` and function files being stored in `functions/function_name.fish`.
