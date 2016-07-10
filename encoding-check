#! /usr/bin/env python3

import sys
import shutil
import argparse


def main():
    config = parse_args()

    try:
        for f in config.files:
            process_file(f, config)
    except KeyboardInterrupt:
        print("<<< Aborted by user! >>>", file=sys.stderr)


def parse_args():
    argparser = argparse.ArgumentParser(description="Show all lines of a FILE containing characters "
                                                    "that don't match the selected ENCODING.")

    argparser.add_argument("files", action="store", nargs="+", metavar="FILE",
                           help="the file to be examined")
    argparser.add_argument("-e", "--encoding", action="store", default="ascii",
                           dest="codec", metavar="ENCODING",
                           help="file encoding to test (default 'ascii')")

    out_group = argparser.add_mutually_exclusive_group()
    out_group.add_argument("-s", "--summary", action="store_const", const="summary",
                           dest="output", help="only print the summary")
    out_group.add_argument("-c", "--count", action="store_const", const="count",
                           dest="output", help="only print the detected line count")
    out_group.add_argument("-l", "--lines", action="store_const", const="lines",
                           dest="output", help="only print the detected lines")

    argparser.add_argument("-m", "--only-matching", action="store_true",
                           default=False, dest="only_matching",
                           help="hide files without matching lines from output")
    argparser.add_argument("-w", "--no-warnings", action="store_false", default=True,
                           dest="warn", help="hide warnings from output")
    argparser.add_argument("-n", "--no-numbers", action="store_false", default=True,
                           dest="lnum", help="do not show line numbers in output")
    argparser.add_argument("-f", "--fit-width", action="store", default=-1,
                           type=int, dest="fit_width", metavar="N",
                           help="trim lines to N characters, or terminal width if "
                                "N=0; non-printable characters like tabs will be removed")
    argparser.add_argument("-t", "--title", action="store_true", default=False,
                           dest="title", help="print title line above each file")
    argparser.set_defaults(output="full")

    args = argparser.parse_args()

    args.max_width = args.fit_width if args.fit_width > 0 \
        else None if args.fit_width < 0 \
        else shutil.get_terminal_size().columns

    return args


def process_file(f, config):
    try:
        with open(f, mode="rb") as file:
            lines = file.readlines()
    except FileNotFoundError:
        if config.warn:
            print("WARNING! The file '{}' does not exist.".format(f), 
                  file=sys.stderr)
        return
    except IsADirectoryError:
        if config.warn:
            print("WARNING! '{}' is a directory. Skipping it."
                  .format(f), file=sys.stderr)
        return
    except PermissionError:
        if config.warn:
            print("WARNING! No permission to read '{}'."
                  .format(f), file=sys.stderr)
        return

    if config.title:
        if f != config.files[0]:
            print()
        print("*" * 20, f, "*" * 20)

    counter = 0
    for n, line in enumerate(lines, 1):
        try:
            l = line.decode(config.codec, "strict")
        except UnicodeDecodeError:
            if config.output in ("full", "lines"):
                output = line.decode(config.codec, "replace")
                if config.lnum:
                    output = "{:>6d}: {}".format(n, output)
                if config.max_width:
                    output = "".join(c for c in output if c.isprintable())
                    print(output[:config.max_width])
                else:
                    print(output)
            counter += 1
        except LookupError:
            print("ERROR! The encoding '{}' is unfortunately not available."
                  .format(config.codec), file=sys.stderr)
            exit(2)

    if not config.only_matching or counter > 0:
        if config.output in ("full", "summary", "quiet"):
            if config.output == "full" and counter > 0:
                print("\n" + "-" * 80)
            print("{filename}: {length} lines in total, {counter} lines have non-{codec} characters."
                  .format(length=len(lines), filename=f, counter=counter, codec=config.codec.upper()))
            if config.output == "full" and counter > 0:
                print()
        elif config.output == "count":
            print(counter)

if __name__ == "__main__":
    main()
