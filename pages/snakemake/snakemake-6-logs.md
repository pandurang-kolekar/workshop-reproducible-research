As you probably noticed it was difficult to follow how the workflow progressed
since some rules printed a lot of output to the terminal. In some cases this
also contained important information, such as statistics on the sequence
alignments or genome indexing. This could be valuable for example if you later
in the project get weird results and want to debug. It's also important from
a reproducibility perspective that the "paper trail" describing how the outputs
were generated is saved. Luckily, Snakemake has a feature that can help with
this. Just as we define `input` and `output` in a rule we can also define
`log`.

```python
rule some_rule:
    input:
        "..."
    output:
        "..."
    log:
        "..."
    shell:
        """
        echo 'Converting {input} to {output}' > {log}
        """
```

A log file is not different from any other output file, but it's dealt with
a little differently by Snakemake. For example, it's shown in the file summary
when using `-D`. It's also a good way to clarify the purpose of the file. We
probably don't need to save logs for all the rules, only the ones with
interesting output.

* `get_genome_fasta` and `get_genome_gff3` would be good to log since they are
  dependent on downloading files from an external server.
* `multiqc` aggregates quality control data for all the samples into one html
  report, and the log contains information about which samples were aggregated.
* `index_genome` outputs some statistics about the genome indexing.
* `align_to_genome` outputs important statistics about the alignments. This is
  probably the most important log to save.

Now add a log file to some or all of the rules above. A good place to save them
to would be `results/logs/rule_name/`. Be sure to include any wildcards used in
the rule in the job name as well, *e.g.* `{some_wildcard}.log`, so that you
don't end up with identical names for different samples.

You also have to specify in the `shell` section of each rule what you want the
log to contain. Some of the programs we use send their log information to
standard out, some to standard error and some let us specify a log file via
a flag. To save some time you can use the info below.

```bash
# Wget has a -o flag for specifying the log file
wget remote_file -O output_file -o {log}

# MultiQC writes to standard error so we redirect with "2>"
multiqc -n output_file input_files 2> {log}

# Bowtie2-build redirects to standard out so we use ">"
bowtie2-build input_file index_dir > {log}

# Bowtie2 writes main output to standard out and the log info to standard error
bowtie2 -x index_dir -U input_file > output_file 2> {log}
```

Now rerun the whole workflow by using the `-F` flag. Do the logs contain what
they should? Note how much easier it is to follow the progression of the
workflow when the rules write to logs instead of to the terminal. If you run
with `-D` (or `-S` for a simpler version) you will see that the summary table
now also contains the log file for each of the files in the workflow.

> **Quick recap** <br>
> In this section we've learned:
>
> - How to redirect output to log files with the `log` directive.