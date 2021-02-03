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
<h3>bcd<a class="headerlink" href="#bcd" title="Permalink to this headline">¶</a></h3>
<p>Bonita Continuous Delivery CLI.</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND <span class="o">[</span>COMMAND_OPTIONS<span class="o">]</span> SUBCOMMAND <span class="o">[</span>SUBCOMMAND_OPTIONS<span class="o">]</span>...
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-s">
<span id="cmdoption-bcd-scenario"></span><code class="sig-name descname">-s</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--scenario</code><code class="sig-prename descclassname"> &lt;scenario&gt;</code><a class="headerlink" href="#cmdoption-bcd-s" title="Permalink to this definition">¶</a></dt>
<dd><p>YAML scenario file path - <strong>MANDATORY OPTION</strong> unless <cite>BCD_SCENARIO</cite> environment variable is defined.</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-y">
<span id="cmdoption-bcd-yes"></span><code class="sig-name descname">-y</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--yes</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-y" title="Permalink to this definition">¶</a></dt>
<dd><p>Execute action without confirmation prompt</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-v">
<span id="cmdoption-bcd-verbose"></span><code class="sig-name descname">-v</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--verbose</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-v" title="Permalink to this definition">¶</a></dt>
<dd><p>Enable Ansible verbose mode</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-nocolor">
<code class="sig-name descname">--nocolor</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-nocolor" title="Permalink to this definition">¶</a></dt>
<dd><p>Turn output colorization off</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-e">
<span id="cmdoption-bcd-extra-vars"></span><code class="sig-name descname">-e</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--extra-vars</code><code class="sig-prename descclassname"> &lt;extra_vars&gt;</code><a class="headerlink" href="#cmdoption-bcd-e" title="Permalink to this definition">¶</a></dt>
<dd><p>Extra vars for Ansible (multiple) - Variables are passed using the key=value syntax.</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-h">
<span id="cmdoption-bcd-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-version">
<code class="sig-name descname">--version</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-version" title="Permalink to this definition">¶</a></dt>
<dd><p>Show the version and exit.</p>
</dd></dl>

<div class="section" id="bcd-license">
<h4>license<a class="headerlink" href="#bcd-license" title="Permalink to this headline">¶</a></h4>
<p>Manage Bonita licenses</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> license <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND <span class="o">[</span>COMMAND_OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-license-h">
<span id="cmdoption-bcd-license-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-license-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

<div class="section" id="bcd-bcd-options-license-generate">
<h5>generate<a class="headerlink" href="#bcd-bcd-options-license-generate" title="Permalink to this headline">¶</a></h5>
<p>Request a license and get the license file</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> license <span class="o">[</span>LICENSE_OPTIONS<span class="o">]</span> generate <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-license-generate-k">
<span id="cmdoption-bcd-bcd-options-license-generate-k"></span><span id="cmdoption-bcd-BCD_OPTIONS-license-generate-request-key"></span><span id="cmdoption-bcd-bcd-options-license-generate-request-key"></span><code class="sig-name descname">-k</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--request-key</code><code class="sig-prename descclassname"> &lt;request_key&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-license-generate-k" title="Permalink to this definition">¶</a></dt>
<dd><p>Request key to generate the license</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-license-generate-o">
<span id="cmdoption-bcd-bcd-options-license-generate-o"></span><span id="cmdoption-bcd-BCD_OPTIONS-license-generate-output-dir"></span><span id="cmdoption-bcd-bcd-options-license-generate-output-dir"></span><code class="sig-name descname">-o</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--output-dir</code><code class="sig-prename descclassname"> &lt;output_dir&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-license-generate-o" title="Permalink to this definition">¶</a></dt>
<dd><p>Output directory path where the license file will be saved - Default to <cite>/tmp</cite></p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-license-generate-h">
<span id="cmdoption-bcd-bcd-options-license-generate-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-license-generate-help"></span><span id="cmdoption-bcd-bcd-options-license-generate-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-license-generate-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-license-revoke">
<h5>revoke<a class="headerlink" href="#bcd-bcd-options-license-revoke" title="Permalink to this headline">¶</a></h5>
<p>Revoke a license file</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> license <span class="o">[</span>LICENSE_OPTIONS<span class="o">]</span> revoke <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-license-revoke-p">
<span id="cmdoption-bcd-bcd-options-license-revoke-p"></span><span id="cmdoption-bcd-BCD_OPTIONS-license-revoke-path"></span><span id="cmdoption-bcd-bcd-options-license-revoke-path"></span><code class="sig-name descname">-p</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--path</code><code class="sig-prename descclassname"> &lt;path&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-license-revoke-p" title="Permalink to this definition">¶</a></dt>
<dd><p>Path to the license file to revoke</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-license-revoke-h">
<span id="cmdoption-bcd-bcd-options-license-revoke-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-license-revoke-help"></span><span id="cmdoption-bcd-bcd-options-license-revoke-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-license-revoke-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
</div>
<div class="section" id="bcd-livingapp">
<h4>livingapp<a class="headerlink" href="#bcd-livingapp" title="Permalink to this headline">¶</a></h4>
<p>Manage Bonita Living Application</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> livingapp <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND1 <span class="o">[</span>COMMAND1_OPTIONS<span class="o">]</span> <span class="o">[</span>COMMAND2
                            <span class="o">[</span>COMMAND2_OPTIONS<span class="o">]]</span>...
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-livingapp-h">
<span id="cmdoption-bcd-livingapp-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-livingapp-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

<div class="section" id="bcd-bcd-options-livingapp-build">
<h5>build<a class="headerlink" href="#bcd-bcd-options-livingapp-build" title="Permalink to this headline">¶</a></h5>
<p>Build Bonita Living Application</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> livingapp <span class="o">[</span>LIVINGAPP_OPTIONS<span class="o">]</span> build <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-build-p">
<span id="cmdoption-bcd-bcd-options-livingapp-build-p"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-build-path"></span><span id="cmdoption-bcd-bcd-options-livingapp-build-path"></span><code class="sig-name descname">-p</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--path</code><code class="sig-prename descclassname"> &lt;path&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-build-p" title="Permalink to this definition">¶</a></dt>
<dd><p>Bonita Living Application repository path</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-build-e">
<span id="cmdoption-bcd-bcd-options-livingapp-build-e"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-build-environment"></span><span id="cmdoption-bcd-bcd-options-livingapp-build-environment"></span><code class="sig-name descname">-e</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--environment</code><code class="sig-prename descclassname"> &lt;environment&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-build-e" title="Permalink to this definition">¶</a></dt>
<dd><p>Name of the process configuration environment as defined in Bonita Studio</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-build-X">
<span id="cmdoption-bcd-bcd-options-livingapp-build-x"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-build-debug"></span><span id="cmdoption-bcd-bcd-options-livingapp-build-debug"></span><code class="sig-name descname">-X</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--debug</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-build-X" title="Permalink to this definition">¶</a></dt>
<dd><p>Enable debug mode</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-build-h">
<span id="cmdoption-bcd-bcd-options-livingapp-build-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-build-help"></span><span id="cmdoption-bcd-bcd-options-livingapp-build-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-build-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-livingapp-deploy">
<h5>deploy<a class="headerlink" href="#bcd-bcd-options-livingapp-deploy" title="Permalink to this headline">¶</a></h5>
<p>Deploy Bonita Living Application</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> livingapp <span class="o">[</span>LIVINGAPP_OPTIONS<span class="o">]</span> deploy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-p">
<span id="cmdoption-bcd-bcd-options-livingapp-deploy-p"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-path"></span><span id="cmdoption-bcd-bcd-options-livingapp-deploy-path"></span><code class="sig-name descname">-p</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--path</code><code class="sig-prename descclassname"> &lt;path&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-p" title="Permalink to this definition">¶</a></dt>
<dd><p>Bonita Living Application archive or directory path</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-c">
<span id="cmdoption-bcd-bcd-options-livingapp-deploy-c"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-configuration-path"></span><span id="cmdoption-bcd-bcd-options-livingapp-deploy-configuration-path"></span><code class="sig-name descname">-c</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--configuration-path</code><code class="sig-prename descclassname"> &lt;configuration_path&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-c" title="Permalink to this definition">¶</a></dt>
<dd><p>Bonita configuration (.bconf) archive path</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-http-timeout">
<span id="cmdoption-bcd-bcd-options-livingapp-deploy-http-timeout"></span><code class="sig-name descname">--http-timeout</code><code class="sig-prename descclassname"> &lt;http_timeout&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-http-timeout" title="Permalink to this definition">¶</a></dt>
<dd><p>Timeout in seconds for HTTP interactions with Bonita stack. The transaction timeout of the platform should be increased accordingly.</p>
<dl class="field-list simple">
<dt class="field-odd">Default</dt>
<dd class="field-odd"><p>120</p>
</dd>
</dl>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-disable-certificate-check">
<span id="cmdoption-bcd-bcd-options-livingapp-deploy-disable-certificate-check"></span><code class="sig-name descname">--disable-certificate-check</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-disable-certificate-check" title="Permalink to this definition">¶</a></dt>
<dd><p>Disable all certificate validation when connecting to a Bonita stack over HTTPS. This option may be used when a self-signed certificate is installed on the target Bonita stack</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-development-mode">
<span id="cmdoption-bcd-bcd-options-livingapp-deploy-development-mode"></span><code class="sig-name descname">--development-mode</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-development-mode" title="Permalink to this definition">¶</a></dt>
<dd><p>Deploy for development environments (eg. processes and profiles are replaced if existing, organization data are merged).</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-X">
<span id="cmdoption-bcd-bcd-options-livingapp-deploy-x"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-debug"></span><span id="cmdoption-bcd-bcd-options-livingapp-deploy-debug"></span><code class="sig-name descname">-X</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--debug</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-X" title="Permalink to this definition">¶</a></dt>
<dd><p>Enable debug mode</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-h">
<span id="cmdoption-bcd-bcd-options-livingapp-deploy-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-help"></span><span id="cmdoption-bcd-bcd-options-livingapp-deploy-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-deploy-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-livingapp-extract-conf">
<h5>extract-conf<a class="headerlink" href="#bcd-bcd-options-livingapp-extract-conf" title="Permalink to this headline">¶</a></h5>
<p>Extract parameters from a Bonita configuration archive (.bconf)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> livingapp <span class="o">[</span>LIVINGAPP_OPTIONS<span class="o">]</span> extract-conf 
    <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-p">
<span id="cmdoption-bcd-bcd-options-livingapp-extract-conf-p"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-path"></span><span id="cmdoption-bcd-bcd-options-livingapp-extract-conf-path"></span><code class="sig-name descname">-p</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--path</code><code class="sig-prename descclassname"> &lt;path&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-p" title="Permalink to this definition">¶</a></dt>
<dd><p>Bonita configuration (.bconf) archive path</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-without-value">
<span id="cmdoption-bcd-bcd-options-livingapp-extract-conf-without-value"></span><code class="sig-name descname">--without-value</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-without-value" title="Permalink to this definition">¶</a></dt>
<dd><p>Only extract parameters which do not have a value</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-o">
<span id="cmdoption-bcd-bcd-options-livingapp-extract-conf-o"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-output"></span><span id="cmdoption-bcd-bcd-options-livingapp-extract-conf-output"></span><code class="sig-name descname">-o</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--output</code><code class="sig-prename descclassname"> &lt;output&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-o" title="Permalink to this definition">¶</a></dt>
<dd><p>Output file</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-h">
<span id="cmdoption-bcd-bcd-options-livingapp-extract-conf-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-help"></span><span id="cmdoption-bcd-bcd-options-livingapp-extract-conf-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-extract-conf-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-livingapp-merge-conf">
<h5>merge-conf<a class="headerlink" href="#bcd-bcd-options-livingapp-merge-conf" title="Permalink to this headline">¶</a></h5>
<p>Merge parameters into a Bonita configuration archive (.bconf)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> livingapp <span class="o">[</span>LIVINGAPP_OPTIONS<span class="o">]</span> merge-conf 
    <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-p">
<span id="cmdoption-bcd-bcd-options-livingapp-merge-conf-p"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-path"></span><span id="cmdoption-bcd-bcd-options-livingapp-merge-conf-path"></span><code class="sig-name descname">-p</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--path</code><code class="sig-prename descclassname"> &lt;path&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-p" title="Permalink to this definition">¶</a></dt>
<dd><p>Bonita configuration (.bconf) archive path</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-i">
<span id="cmdoption-bcd-bcd-options-livingapp-merge-conf-i"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-input"></span><span id="cmdoption-bcd-bcd-options-livingapp-merge-conf-input"></span><code class="sig-name descname">-i</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--input</code><code class="sig-prename descclassname"> &lt;input&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-i" title="Permalink to this definition">¶</a></dt>
<dd><p>Input parameter file</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-o">
<span id="cmdoption-bcd-bcd-options-livingapp-merge-conf-o"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-output"></span><span id="cmdoption-bcd-bcd-options-livingapp-merge-conf-output"></span><code class="sig-name descname">-o</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--output</code><code class="sig-prename descclassname"> &lt;output&gt;</code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-o" title="Permalink to this definition">¶</a></dt>
<dd><p>Output file</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-h">
<span id="cmdoption-bcd-bcd-options-livingapp-merge-conf-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-help"></span><span id="cmdoption-bcd-bcd-options-livingapp-merge-conf-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-livingapp-merge-conf-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
</div>
<div class="section" id="bcd-stack">
<h4>stack<a class="headerlink" href="#bcd-stack" title="Permalink to this headline">¶</a></h4>
<p>Manage Bonita stack (infrastructure)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>OPTIONS<span class="o">]</span> COMMAND1 <span class="o">[</span>COMMAND1_OPTIONS<span class="o">]</span> <span class="o">[</span>COMMAND2
                        <span class="o">[</span>COMMAND2_OPTIONS<span class="o">]]</span>...
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-stack-h">
<span id="cmdoption-bcd-stack-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-stack-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

<div class="section" id="bcd-bcd-options-stack-create">
<h5>create<a class="headerlink" href="#bcd-bcd-options-stack-create" title="Permalink to this headline">¶</a></h5>
<p>Create infra machines</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>STACK_OPTIONS<span class="o">]</span> create <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-stack-create-d">
<span id="cmdoption-bcd-bcd-options-stack-create-d"></span><span id="cmdoption-bcd-BCD_OPTIONS-stack-create-dry-run"></span><span id="cmdoption-bcd-bcd-options-stack-create-dry-run"></span><code class="sig-name descname">-d</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--dry-run</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-stack-create-d" title="Permalink to this definition">¶</a></dt>
<dd><p>Show an execution plan only</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-stack-create-h">
<span id="cmdoption-bcd-bcd-options-stack-create-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-stack-create-help"></span><span id="cmdoption-bcd-bcd-options-stack-create-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-stack-create-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-stack-deploy">
<h5>deploy<a class="headerlink" href="#bcd-bcd-options-stack-deploy" title="Permalink to this headline">¶</a></h5>
<p>Deploy Bonita stack (creates Docker containers)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>STACK_OPTIONS<span class="o">]</span> deploy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-stack-deploy-h">
<span id="cmdoption-bcd-bcd-options-stack-deploy-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-stack-deploy-help"></span><span id="cmdoption-bcd-bcd-options-stack-deploy-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-stack-deploy-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-stack-destroy">
<h5>destroy<a class="headerlink" href="#bcd-bcd-options-stack-destroy" title="Permalink to this headline">¶</a></h5>
<p>Destroy infra machines</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>STACK_OPTIONS<span class="o">]</span> destroy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-stack-destroy-d">
<span id="cmdoption-bcd-bcd-options-stack-destroy-d"></span><span id="cmdoption-bcd-BCD_OPTIONS-stack-destroy-dry-run"></span><span id="cmdoption-bcd-bcd-options-stack-destroy-dry-run"></span><code class="sig-name descname">-d</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--dry-run</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-stack-destroy-d" title="Permalink to this definition">¶</a></dt>
<dd><p>Show an execution plan only</p>
</dd></dl>

<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-stack-destroy-h">
<span id="cmdoption-bcd-bcd-options-stack-destroy-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-stack-destroy-help"></span><span id="cmdoption-bcd-bcd-options-stack-destroy-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-stack-destroy-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-stack-status">
<h5>status<a class="headerlink" href="#bcd-bcd-options-stack-status" title="Permalink to this headline">¶</a></h5>
<p>Show the platform status</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>STACK_OPTIONS<span class="o">]</span> status <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-stack-status-h">
<span id="cmdoption-bcd-bcd-options-stack-status-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-stack-status-help"></span><span id="cmdoption-bcd-bcd-options-stack-status-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-stack-status-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
<div class="section" id="bcd-bcd-options-stack-undeploy">
<h5>undeploy<a class="headerlink" href="#bcd-bcd-options-stack-undeploy" title="Permalink to this headline">¶</a></h5>
<p>Undeploy Bonita stack (removes Docker containers)</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd <span class="o">[</span>BCD_OPTIONS<span class="o">]</span> stack <span class="o">[</span>STACK_OPTIONS<span class="o">]</span> undeploy <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-BCD_OPTIONS-stack-undeploy-h">
<span id="cmdoption-bcd-bcd-options-stack-undeploy-h"></span><span id="cmdoption-bcd-BCD_OPTIONS-stack-undeploy-help"></span><span id="cmdoption-bcd-bcd-options-stack-undeploy-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-BCD_OPTIONS-stack-undeploy-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
</div>
<div class="section" id="bcd-version">
<h4>version<a class="headerlink" href="#bcd-version" title="Permalink to this headline">¶</a></h4>
<p>Show BCD version information</p>
<div class="highlight-shell notranslate"><div class="highlight"><pre><span></span>bcd version <span class="o">[</span>OPTIONS<span class="o">]</span>
</pre></div>
</div>
<p class="rubric">Options</p>
<dl class="std option">
<dt id="cmdoption-bcd-version-h">
<span id="cmdoption-bcd-version-help"></span><code class="sig-name descname">-h</code><code class="sig-prename descclassname"></code><code class="sig-prename descclassname">, </code><code class="sig-name descname">--help</code><code class="sig-prename descclassname"></code><a class="headerlink" href="#cmdoption-bcd-version-h" title="Permalink to this definition">¶</a></dt>
<dd><p>Show this help message</p>
</dd></dl>

</div>
</div>
