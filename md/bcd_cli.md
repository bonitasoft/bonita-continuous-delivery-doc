# BCD command-line interface

On this page you will find detailed information about the BCD CLI with usage examples and reference.


## Requirements

* First the `bcd` command requires that the **`BCD_HOME`** environment variable is set.  
By default `BCD_HOME` is set to `/home/bonita/bonita-continuous-delivery` in the controller container.
* Next the `bcd` command takes a mandatory option called `scenario` through the `-s / --scenario` command line option.  
This option expects an existing relative or absolute path to a [BCD YAML scenario](scenarios.md) file.  
The BCD scenario path can also be specified through the **`BCD_SCENARIO`** environment variable, hence allowing to skip the `-s / --scenario` command line option.
* Finally the BCD CLI expects to find Bonita version-specific dependencies in the **`$BCD_HOME/dependencies`** directory. This directory must contain:
    - `bonita-subscription_*.tar.gz` - (Mandatory) The Bonita Docker image archive
    - `bonita-la-builder-*-exec.jar` - (Mandatory) The Bonita Living Application Builder library
    - `bonita-sp-*-maven-repository.zip` - (Optional) The Bonita Maven repository zip if required by your Living Application workspace.


## Usage examples

Running the `create` subcommand of the `stack` command with scenario command-line option:
```
$ bcd -s scenarios/uswest2_cluster.yml stack create
```

Running the `deploy` subcommand of the `stack` command with scenario command-line option and overridden `mail_notification` variable:
```
$ bcd -s scenarios/vagrant_single.yml -e mail_notification=no -y stack deploy
```

Running the `destroy` subcommand of the `stack` command with `BCD_SCENARIO` environment variable and subcommand `--dry-run` option:
```
$ export BCD_SCENARIO=scenarios/uswest2_performance.yml
$ bcd stack destroy --dry-run
```

Running the `build` subcommand of the `livingapp` command with `BCD_SCENARIO` environment variable and subcommand `-p` and `-e` options:
```
$ export BCD_SCENARIO=scenarios/euwest1_performance.yml
$ bcd livingapp build -p path/to/livingapp-repo -e Qualification
```


## Multi command chaining

The BCD CLI allows to invoke more than one command in one go. This is useful to chain commands as a pipeline.

If one command fails then `bcd` stops executing any subsequent command it may have.

**Examples**:

Running `create` then `deploy` subcommands of the `stack` command:
```
$ bcd -s scenarios/uswest2_cluster.yml -y stack create deploy
```

Running `build` then `deploy` subcommands of the `livingapp` command:
```
$ bcd -s scenarios/vagrant_single.yml --yes \
    livingapp build -p path/to/livingapp-repo -e Qualification deploy
```


## Command-line reference

This section describes the comprehensive usage information for the `bcd` command.

You can also see this information by running `bcd --help` and `bcd [SUBCOMMAND] --help` - where SUBCOMMAND is in `[livingapp, stack]` - from the command line.

<div class="section" id="bcd">
<span class="h2">bcd</span>
<p>Bonita Continuous Delivery CLI.</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND <span class="o">[</span>ARGS<span class="o">]</span>...
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-v">
<code class="descname">-v</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--verbose</code><code class="descclassname"></code></dt>
<dd><p>Enable Ansible verbose mode</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-y">
<code class="descname">-y</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--yes</code><code class="descclassname"></code></dt>
<dd><p>Execute action without confirmation prompt</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-nocolor">
<code class="descname">--nocolor</code><code class="descclassname"></code></dt>
<dd><p>Turn output colorization off</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-s">
<code class="descname">-s</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--scenario</code><code class="descclassname"> &lt;scenario&gt;</code></dt>
<dd><p>YAML scenario file path - Optional if <cite>BCD_SCENARIO</cite> environment variable is defined.</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-e">
<code class="descname">-e</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--extra-vars</code><code class="descclassname"> &lt;extra_vars&gt;</code></dt>
<dd><p>Extra vars for Ansible (multiple) - Variables are passed using the key=value syntax.</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

<div class="section" id="bcd-livingapp">
<span class="h3">livingapp</span>
<p>Manage Bonita Living Application</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd livingapp <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND1 <span class="o">[</span>ARGS<span class="o">]</span>... <span class="o">[</span>COMMAND2 <span class="o">[</span>ARGS<span class="o">]</span>...<span class="o">]</span>...
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-livingapp-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

<div class="section" id="bcd-livingapp-build">
<span class="h4">build</span>
<p>Build Bonita Living Application</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd livingapp build <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-livingapp-build-p">
<code class="descname">-p</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--path</code><code class="descclassname"> &lt;path&gt;</code></dt>
<dd><p>Bonita Living Application workspace path</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-livingapp-build-e">
<code class="descname">-e</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--environment</code><code class="descclassname"> &lt;environment&gt;</code></dt>
<dd><p>Name of the process configuration environment as defined in Bonita Studio</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-livingapp-build-x">
<code class="descname">-X</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--debug</code><code class="descclassname"></code></dt>
<dd><p>Enable debug mode</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-livingapp-build-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-livingapp-deploy">
<span class="h4">deploy</span>
<p>Deploy Bonita Living Application</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd livingapp deploy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-livingapp-deploy-p">
<code class="descname">-p</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--path</code><code class="descclassname"> &lt;path&gt;</code></dt>
<dd><p>Bonita Living Application file path</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-livingapp-deploy-x">
<code class="descname">-X</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--debug</code><code class="descclassname"></code></dt>
<dd><p>Enable debug mode</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-livingapp-deploy-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
</div>
<div class="section" id="bcd-stack">
<span class="h3">stack</span>
<p>Manage Bonita stack (infrastructure)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd stack <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND1 <span class="o">[</span>ARGS<span class="o">]</span>... <span class="o">[</span>COMMAND2 <span class="o">[</span>ARGS<span class="o">]</span>...<span class="o">]</span>...
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-stack-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

<div class="section" id="bcd-stack-create">
<span class="h4">create</span>
<p>Create AWS instances</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd stack create <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-stack-create-d">
<code class="descname">-d</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--dry-run</code><code class="descclassname"></code></dt>
<dd><p>Show an execution plan only</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-stack-create-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-stack-deploy">
<span class="h4">deploy</span>
<p>Deploy Bonita stack (creates Docker containers)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd stack deploy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-stack-deploy-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-stack-destroy">
<span class="h4">destroy</span>
<p>Destroy AWS machines (EC2 instances)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd stack destroy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-stack-destroy-d">
<code class="descname">-d</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--dry-run</code><code class="descclassname"></code></dt>
<dd><p>Show an execution plan only</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-stack-destroy-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-stack-status">
<span class="h4">status</span>
<p>Show the platform status</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd stack status <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-stack-status-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-stack-undeploy">
<span class="h4">undeploy</span>
<p>Undeploy Bonita stack (removes Docker containers)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd stack undeploy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-stack-undeploy-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
</div>
</div>
