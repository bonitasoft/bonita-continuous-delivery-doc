# Release notes

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```


## What's new in 3.3.0 (2020-03-01)

### Breaking changes

* With release 3.3.0, BCD introduces deployment modes (_Development_ and _Production_) to manage artifacts deployment policies more easily.

::: warning
The default deployment policies are changing from the previous BCD versions. 
The new deployment policies are more conservative:

|                          |3.2.1 and previous versions                          |3.3.0 and later versions                                                 |
|--------------------------|-----------------------------------------------------|-------------------------------------------------------|
|Default deployment mode   |Development                                          |Production                                             |
|Applications              |<code class="descclassname">REPLACE_DUPLICATES</code>|<code class="descclassname">REPLACE_DUPLICATES</code>  |
|Organization              |<code class="descclassname">MERGE_DUPLICATES</code>  |<code class="descclassname">IGNORE_DUPLICATES</code>   |
|Processes                 |<code class="descclassname">REPLACE_DUPLICATES</code>|<code class="descclassname">IGNORE_DUPLICATES</code>   |
|Profiles                  |<code class="descclassname">REPLACE_DUPLICATES</code>|<code class="descclassname">IGNORE_IF_ANY_EXISTS</code>|

:::
