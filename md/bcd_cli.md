# BCD command-line interface

On this page you will find detailed information about the BCD CLI with usage examples and reference.


## Requirements

* First, the `bcd` command requires that the **`BCD_HOME`** environment variable is set.
By default `BCD_HOME` is set to `/home/bonita/bonita-continuous-delivery` in the controller container.
* Next, the `bcd` command takes a ***mandatory option*** called `scenario` through the `-s / --scenario` command line option.
This option expects an existing relative or absolute path to a [BCD YAML scenario](scenarios.md) file.
The BCD scenario path can also be specified through the **`BCD_SCENARIO`** environment variable, hence allowing to skip the `-s / --scenario` command line option.
* Finally, the BCD CLI expects to find Bonita version-specific dependencies in the **`$BCD_HOME/dependencies`** directory or any of its subdirectories. BCD dependencies include:
    - `bonita-la-builder-*-exec.jar` - (Mandatory) The Bonita Living Application Builder library
    - `bonita-sp-*-maven-repository.zip` - (Optional) The Bonita Maven repository zip if required by your Living Application workspace.
Since BCD 3.0.0, dependencies can be retrieved as `quay.io/bonitasoft/bcd-dependencies:<bonita_version>` Docker images and they must be mounted as volumes in the `$BCD_HOME/dependencies` directory.
You can read more about in [Common installation steps](getting_started.md#toc1)


## Usage examples

Running the `create` subcommand of the `stack` command with scenario command-line option:
```bash
$ bcd -s scenarios/uswest2_cluster.yml stack create
```

Running the `deploy` subcommand of the `stack` command with scenario command-line option and overridden `mail_notification` variable:
```bash
$ bcd -s scenarios/vagrant_single.yml -e mail_notification=no -y stack deploy
```

Running the `destroy` subcommand of the `stack` command with `BCD_SCENARIO` environment variable and subcommand `--dry-run` option:
```bash
$ export BCD_SCENARIO=scenarios/uswest2_performance.yml
$ bcd stack destroy --dry-run
```

Running the `build` subcommand of the `livingapp` command with `BCD_SCENARIO` environment variable and subcommand `-p` and `-e` options:
```bash
$ export BCD_SCENARIO=scenarios/euwest1_performance.yml
$ bcd livingapp build -p path/to/livingapp-repo -e Test
```

Running the `version` command which does not require a scenario file:
```bash
$ bcd version
```


## Multi command chaining

The BCD CLI allows to invoke more than one command in one go. This is useful to chain commands as a pipeline.

If one command fails then `bcd` stops executing any subsequent command it may have.

**Examples**:

Running `create` then `deploy` subcommands of the `stack` command:
```bash
$ bcd -s scenarios/uswest2_cluster.yml -y stack create deploy
```

Running `build` then `deploy` subcommands of the `livingapp` command:
```bash
$ bcd -s scenarios/vagrant_single.yml --yes \
    livingapp build -p path/to/livingapp-repo -e Test deploy
```


## Command-line reference

This section describes the comprehensive usage information for the `bcd` command.

You can also see this information by running `bcd --help` and `bcd [COMMAND] --help` - where COMMAND is in `[license, livingapp, stack, version]` - from the command line.

<div class="section" id="bcd">
<span class="h3">bcd</span>
<p>Bonita Continuous Delivery CLI.</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND <span class="o">[</span>COMMAND_OPTIONS<span class="o">]</span> SUBCOMMAND <span class="o">[</span>SUBCOMMAND_OPTIONS<span class="o">]</span>...
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-s">
<code class="descname">-s</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--scenario</code><code class="descclassname"> &lt;scenario&gt;</code></dt>
<dd><p>YAML scenario file path - <strong>MANDATORY OPTION</strong> unless <cite>BCD_SCENARIO</cite> environment variable is defined.</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-y">
<code class="descname">-y</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--yes</code><code class="descclassname"></code></dt>
<dd><p>Execute action without confirmation prompt</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-v">
<code class="descname">-v</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--verbose</code><code class="descclassname"></code></dt>
<dd><p>Enable Ansible verbose mode</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-nocolor">
<code class="descname">--nocolor</code><code class="descclassname"></code></dt>
<dd><p>Turn output colorization off</p>
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

<dl class="option">
<dt id="cmdoption-bcd-version">
<code class="descname">--version</code><code class="descclassname"></code></dt>
<dd><p>Show the version and exit.</p>
</dd></dl>

<div class="section" id="bcd-license">
<h4>license</h4>
<p>Manage Bonita licenses</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> license <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND <span class="o">[</span>COMMAND_OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-license-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

<div class="section" id="bcd-bcd-options-license-generate">
<h5>generate</h5>
<p>Request a license and get the license file</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> license <span class="o">[</span>LICENSE_OPTIONS<span class="o">]</span> generate <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-license-generate-k">
<code class="descname">-k</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--request-key</code><code class="descclassname"> &lt;request_key&gt;</code></dt>
<dd><p>Request key to generate the license</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-license-generate-o">
<code class="descname">-o</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--output-dir</code><code class="descclassname"> &lt;output_dir&gt;</code></dt>
<dd><p>Output directory path where the license file will be saved - Default to <cite>/tmp</cite></p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-license-generate-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-license-revoke">
<h5>revoke</h5>
<p>Revoke a license file</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> license <span class="o">[</span>LICENSE_OPTIONS<span class="o">]</span> revoke <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-license-revoke-p">
<code class="descname">-p</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--path</code><code class="descclassname"> &lt;path&gt;</code></dt>
<dd><p>Path to the license file to revoke</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-license-revoke-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
</div>
<div class="section" id="bcd-livingapp">
<h4>livingapp</h4>
<p>Manage Bonita Living Application</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> livingapp <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND1 <span class="o">[</span>COMMAND1_OPTIONS<span class="o">]</span> <span class="o">[</span>COMMAND2
                            <span class="o">[</span>COMMAND2_OPTIONS<span class="o">]]</span>...
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-livingapp-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

<div class="section" id="bcd-bcd-options-livingapp-build">
<h5>build</h5>
<p>Build Bonita Living Application</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> livingapp <span class="o">[</span>LIVINGAPP_OPTIONS<span class="o">]</span> build <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-build-p">
<code class="descname">-p</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--path</code><code class="descclassname"> &lt;path&gt;</code></dt>
<dd><p>Bonita Living Application repository path</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-build-e">
<code class="descname">-e</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--environment</code><code class="descclassname"> &lt;environment&gt;</code></dt>
<dd><p>Name of the process configuration environment as defined in Bonita Studio</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-build-x">
<code class="descname">-X</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--debug</code><code class="descclassname"></code></dt>
<dd><p>Enable debug mode</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-build-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-livingapp-deploy">
<h5>deploy</h5>
<p>Deploy Bonita Living Application</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> livingapp <span class="o">[</span>LIVINGAPP_OPTIONS<span class="o">]</span> deploy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-deploy-p">
<code class="descname">-p</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--path</code><code class="descclassname"> &lt;path&gt;</code></dt>
<dd><p>Bonita Living Application archive or directory path</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-deploy-c">
<code class="descname">-c</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--configuration-path</code><code class="descclassname"> &lt;configuration_path&gt;</code></dt>
<dd><p>Bonita configuration (.bconf) archive path</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-deploy-http-timeout">
<code class="descname">--http-timeout</code><code class="descclassname"> &lt;http_timeout&gt;</code></dt>
<dd><p>Timeout in seconds for HTTP interactions with Bonita stack  [default: 120]</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-deploy-disable-certificate-check">
<code class="descname">--disable-certificate-check</code><code class="descclassname"></code></dt>
<dd><p>Disable all certificate validation when connecting to a Bonita stack over HTTPS. This option may be used when a self-signed certificate is installed on the target Bonita stack</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-deploy-x">
<code class="descname">-X</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--debug</code><code class="descclassname"></code></dt>
<dd><p>Enable debug mode</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-deploy-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-livingapp-extract-conf">
<h5>extract-conf</h5>
<p>Extract parameters from a Bonita configuration archive (.bconf)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> livingapp <span class="o">[</span>LIVINGAPP_OPTIONS<span class="o">]</span> extract-conf 
    <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-extract-conf-p">
<code class="descname">-p</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--path</code><code class="descclassname"> &lt;path&gt;</code></dt>
<dd><p>Bonita configuration (.bconf) archive path</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-extract-conf-without-value">
<code class="descname">--without-value</code><code class="descclassname"></code></dt>
<dd><p>Only extract parameters which do not have a value</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-extract-conf-o">
<code class="descname">-o</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--output</code><code class="descclassname"> &lt;output&gt;</code></dt>
<dd><p>Output file</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-extract-conf-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-livingapp-merge-conf">
<h5>merge-conf</h5>
<p>Merge parameters into a Bonita configuration archive (.bconf)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> livingapp <span class="o">[</span>LIVINGAPP_OPTIONS<span class="o">]</span> merge-conf 
    <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-merge-conf-p">
<code class="descname">-p</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--path</code><code class="descclassname"> &lt;path&gt;</code></dt>
<dd><p>Bonita configuration (.bconf) archive path</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-merge-conf-i">
<code class="descname">-i</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--input</code><code class="descclassname"> &lt;input&gt;</code></dt>
<dd><p>Input parameter file</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-merge-conf-o">
<code class="descname">-o</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--output</code><code class="descclassname"> &lt;output&gt;</code></dt>
<dd><p>Output file</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-livingapp-merge-conf-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
</div>
<div class="section" id="bcd-stack">
<h4>stack</h4>
<p>Manage Bonita stack (infrastructure)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND1 <span class="o">[</span>COMMAND1_OPTIONS<span class="o">]</span> <span class="o">[</span>COMMAND2
                        <span class="o">[</span>COMMAND2_OPTIONS<span class="o">]]</span>...
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-stack-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

<div class="section" id="bcd-bcd-options-stack-create">
<h5>create</h5>
<p>Create infra machines</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>STACK_OPTIONS<span class="o">]</span> create <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-stack-create-d">
<code class="descname">-d</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--dry-run</code><code class="descclassname"></code></dt>
<dd><p>Show an execution plan only</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-stack-create-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-stack-deploy">
<h5>deploy</h5>
<p>Deploy Bonita stack (creates Docker containers)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>STACK_OPTIONS<span class="o">]</span> deploy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-stack-deploy-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-stack-destroy">
<h5>destroy</h5>
<p>Destroy infra machines</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>STACK_OPTIONS<span class="o">]</span> destroy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-stack-destroy-d">
<code class="descname">-d</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--dry-run</code><code class="descclassname"></code></dt>
<dd><p>Show an execution plan only</p>
</dd></dl>

<dl class="option">
<dt id="cmdoption-bcd-bcd-options-stack-destroy-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-stack-status">
<h5>status</h5>
<p>Show the platform status</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>STACK_OPTIONS<span class="o">]</span> status <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-stack-status-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-stack-undeploy">
<h5>undeploy</h5>
<p>Undeploy Bonita stack (removes Docker containers)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>STACK_OPTIONS<span class="o">]</span> undeploy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-bcd-options-stack-undeploy-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
</div>
<div class="section" id="bcd-version">
<h4>version</h4>
<p>Show BCD version information</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd version <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="option">
<dt id="cmdoption-bcd-version-h">
<code class="descname">-h</code><code class="descclassname"></code><code class="descclassname">, </code><code class="descname">--help</code><code class="descclassname"></code></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
</div>
