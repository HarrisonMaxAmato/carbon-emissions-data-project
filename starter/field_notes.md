# Data Literacy Field Notes & Terminal Log

**The scenario:** A climate-and-energy nonprofit wants to use this
country-level energy and emissions dataset to help decide where to direct
community-protection and climate-support efforts — but the file just
landed with zero documentation. Before anyone can trust a decision built
on top of it, you need to figure out what's actually in it — what each
column really holds, and anything that looks off. That's what this whole
file walks through: the real, everyday first pass any analyst runs on an
unfamiliar file, and the real answers you need before this data is safe
to act on.

Dataset: `../data/co2_emissions_2022.csv` (see `../data/SOURCE.md`). Each
section below describes what you're trying to find out — **figuring out
which command (and which flags) actually gets you there is part of what
this project tests**, so the exact command isn't given. Where a command
belongs, you'll see a `# TODO:` comment describing the goal instead of a
ready-to-run command; replace the comment with the real command you used.
Paste the **real output** you got underneath, then write your answer based
on what you actually saw, not what you'd expect. Work through this in
order; open the raw file before anything else.

Run all commands from the repo root — the top-level folder you land in
after cloning *your own* copy of this repo (`git clone ...` then `cd
your-repo-name`, see `README.md` Step 3; not this template) — or `cd` into
`data/` first and drop the `data/` prefix — your choice, just be
consistent.

**This one file is your whole record** — the commands you ran, their real
output, and your written answers, all together in the order you actually
did the work. There's no separate log file to keep in sync with this one;
whenever you write a command down anywhere in this file, use a fenced code
block tagged `bash`, like this:

````
```bash
(this part I had forgotten and I had no way to see exactly what I did as I had to get rid of my old copy of this, it was downloaded incorrectly)
download the folder
git init
git status
git add carbon-emissions-data-project-main
git commit -m 
```
````

Three backticks, the word `bash`, your command, three backticks to close —
it renders with syntax highlighting and makes the command copy-pasteable.

## Setup log

Before jumping into the dataset, log the commands you actually ran to set
this repo up (see `README.md`) — `git init`/clone, creating your
`.gitignore`, your first commit. Add to this as you go, don't
reconstruct it from memory at the end.

```bash
git init 
```

## 1. First look at the raw file

Before trusting anything about this file's structure, you want a quick,
safe peek at it — not the whole 254-row file, just the **first 5 lines**,
enough to see the shape of it without scrolling past everything.

```bash
cd carbon-emissions-data-project-main/
ls
cd data
ls
head -n 5 co2_emissions_2022.csv
base) MARCY-MBAir63:data Marcy_Student$ head -n 5 co2_emissions_2022.csv
country,iso_code,year,population,gdp,co2
Afghanistan,AFG,2022,40578847,53303471617,10.170
Africa,,2022,1445862695,,1459.114
Africa (GCP),,2022,,,1459.103
Albania,ALB,2022,2827614,36171005594,4.498
```

**Output:**
_TODO: paste what you actually got_

**What I notice:** _TODO — your raw observations, in your own words_

## 2. Column names

Next you want to see just the column names by themselves — one per line is
a lot easier to scan than one long comma-separated row.

```bash
I tried using ai to come up with a solution to find the most well organized display I could for easier veiwing.
column -s, -t -n co2_emissions_2022.csv | awk '{print $0 "\n"}'

sed 's/,,/, ,/g; s/,,/, ,/g' co2_emissions_2022.csv | column -s, -t | awk '{print $0 "\n"}'
awk -F, '{printf "%-42s %-5s %-5s %-12s %-15s %-8s\n\n", $1, $2, $3, $4, $5, $6}' co2_emissions_2022.csv
```

**Output:**
_TODO_

## 3. Column-by-column data type table

For each column, you want to preview real values — **the first 6 values**
of just that one column — without scrolling through every field on every
row. Column numbers for reference: `country`=1, `iso_code`=2, `year`=3,
`population`=4, `gdp`=5, `co2`=6.

```bash
cut -d, -f1 co2_emissions_2022.csv | head -n 6
cut -d, -f2 co2_emissions_2022.csv | head -n 6
cut -d, -f3 co2_emissions_2022.csv | head -n 6
cut -d, -f4 co2_emissions_2022.csv | head -n 6
cut -d, -f5 co2_emissions_2022.csv | head -n 6
cut -d, -f6 co2_emissions_2022.csv | head -n 6
```

Fill in the table from what you actually saw — not a guess from the column
name alone:

| Column | Inferred type | How I can tell |
|Country | String |It is text|
| Iso code| String | I assume these are similar to zip codes in how they function |
| Year | Integer | a whole number that cannot be a decimal|
| Population| Integer | a whole number that cannot be a decimal |
|  | gdp | wasnt completely sure but i believe it has to be a whole number|
| co2 | Float | co2 levels can have decimals in them|

**In your own words:** what's the actual difference between a list/array
and a table? (Not a definition you looked up — explain it the way you'd
explain it to a friend who's never coded. No command needed for this one,
just think about the table you just filled in above.)
# Without looking it up im not to sure i believe an array is a single column where a list can comprise of multiple columns?
_TODO_

## 4. Structured or unstructured?

State whether this dataset is structured or unstructured, and why.
# It is structured into multiple data types coming together to form a bigger picture

_TODO_

## 5. Before you trust this data

Your first look (see section 1) turned up a row with a suspiciously
**blank** `iso_code`. Before you build anything on this data, you want to
know exactly how many of the 254 rows share that blank — is it a one-off,
or a real pattern worth flagging before anyone draws conclusions from it?
You may need more than one command for this — piping (`|`) feeds one
command's output into the next as input.


```bash
# awk -F, '$2 == "" {print $1; count++} END {print "\nTotal Blank ISOs: " count}' co2_emissions_2022.csv
# 36 blank ISO

```

**Output:**
_TODO_

```bash
# awk -F, '$5 == "" {print}' co2_emissions_2022.csv | wc -l
# 89
```

**Output:**
_TODO_

List **at least 2** specific things you'd want to know about how this
dataset was collected, before trusting any conclusion drawn from it — ground
at least one in the counts you just found.

**My 2 concerns:**
1. Why is such a massive amount of the gdp data missing seems like such a big gap that it is almost intentional
2. was the lack of iso codes oversight in poor data collection or did it cover countries that might not need an iso code for example scotland uses a subdivision iso code that is under the United Kingdoms Iso code.

## 6. Data-quality question for the source

Take the **most specific and checkable** of the two concerns above, and
phrase it as a real question you'd send to the data's source/maintainer. A
good test: could they actually answer it with a concrete yes/no or fact —
not "is this data good?"

# I dont really understand how there is that many missing ISO codes seems like something thats very public information with no possible reason to hide it.

## 7. Something you're actually curious about

Different question from #6 — that one was about checking the data's
trustworthiness. This one's about you: what's something you're genuinely
curious to explore using this data? Pick two specific countries you're
curious to compare, and look up their full rows.

Could it be that there are so many missing gdps because they want to keep their gdp low to be eligable to retain developing country status for foreign aid, or maybe they were sanctioned and trying to hide the effectiveness of the sanctions?
```bash
# 
# awk -F, '$1 == "Kosovo" {printf "%-15s %-5s %-5s %-12s %-15s %-8s\n\n", $1, $2, $3, $4, $5, $6}' co2_emissions_2022.csv

# awk -F, '$1 ~ /^Kuwaiti Oil Fires/ {printf "%-30s %-5s %-5s %-12s %-15s %-8s\n\n", $1, $2, $3, $4, $5, $6}' co2_emissions_2022.csv

```

**Output:**
Kosovo                2022  1717950                      8.569
Kuwaiti Oil Fires (GCP)              2022                               0.000
**My question:** why is kuwaiti oil fires even on here while kuwaiti is a place kuwaiti oil fires describes an environmental disaster

## 8. AI-literacy reflection

Name **2 concrete risks** of trusting an AI assistant's output about this
dataset (or any data question) without checking it yourself. Then state
**one specific personal rule** for when you'll double-check an AI-provided
fact before relying on it professionally — specific enough that you could
actually follow it, not "I'll be careful."

**Risks:**
1. When asking facts about countries you have to double check it because they can be points of contention that the ai registers one side as fact and so you lose the ambiguity and uncertaintity that was initially ment to be conveyed
2. When asked for commands it might over complicate them and make them much longer then needed.

**My rule:** Triple check that the ai understands what im asking so I dont accidently misconvey my questions, and try and improve how much context and detail are in each question to improve quality of answers.

## 9. Something that went wrong (and how you figured it out)

Somewhere in this project you're going to run a command that either throws
an error or gives you output that isn't what you expected — everyone hits
this at least once. Paste the actual command and what actually happened
(not a paraphrase), then explain what that told you and how it led to the
fix — not "I googled it and it worked."

```bash
# sed 's/,,/, ,/g; s/,,/, ,/g' co2_emissions_2022.csv | column -s, -t | awk '{print $0 "\n"}'
```

**What happened:**
It misdisplayed some of the numbers and cut out part of their data which could have drastic impacts if thats not realized.

**What it told me and how I fixed it:** Im not exactly sure how it changed but the command is entirely different and allowed much more consise display of text and numbers.

To find the real number of countries I used the command 
# echo "=== Missing GDP Breakdown ===" && echo -n "Real Countries: " && awk -F, '$5 == "" && $2 != "" {print}' co2_emissions_2022.csv | wc -l && echo -n "Aggregate Rows: " && awk -F, '$5 == "" && $2 == "" {print}' co2_emissions_2022.csv | wc -l
This gives us the number of real countries which is 54