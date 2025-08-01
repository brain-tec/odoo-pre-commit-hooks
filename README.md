[//]: # (start-badges)

[![Build Status](https://github.com/OCA/odoo-pre-commit-hooks/actions/workflows/test.yml/badge.svg?branch=main)](https://github.com/OCA/odoo-pre-commit-hooks/actions/workflows/test.yml?query=branch%3Amain)
[![codecov](https://codecov.io/gh/OCA/odoo-pre-commit-hooks/branch/main/graph/badge.svg)](https://codecov.io/gh/OCA/odoo-pre-commit-hooks)
[![version](https://img.shields.io/pypi/v/oca-odoo-pre-commit-hooks.svg)](https://pypi.org/project/oca-odoo-pre-commit-hooks)
[![wheel](https://img.shields.io/pypi/wheel/oca-odoo-pre-commit-hooks.svg)](https://pypi.org/project/oca-odoo-pre-commit-hooks)
[![supported-versions](https://img.shields.io/pypi/pyversions/oca-odoo-pre-commit-hooks.svg)](https://pypi.org/project/oca-odoo-pre-commit-hooks)
[![commits-since](https://img.shields.io/github/commits-since/OCA/odoo-pre-commit-hooks/v0.1.6.svg)](https://github.com/OCA/odoo-pre-commit-hooks/compare/v0.1.6...main)
[![code-style-black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

[//]: # (end-badges)

# odoo-pre-commit-hooks

OCA's custom pre-commit hooks for Odoo modules


# Installation

You don't need to install it directly only configure your ".pre-commit-config.yaml" file

You even can install it directly:
 - Installing from pypi:
   - `pip install -U oca-odoo-pre-commit-hooks`

 - Installing from github:
   - `pip install --force-reinstall -U git+https://github.com/OCA/odoo-pre-commit-hooks.git@main`


# Usage pre-commit-config.yaml

Add to your ".pre-commit-config.yaml" configuration file the following input


```yaml
    - repo: https://github.com/OCA/odoo-pre-commit-hooks
        rev: v0.1.6
        hooks:
        - id: oca-checks-odoo-module
        - id: oca-checks-po
          args: ["--fix"]
```

# Usage directly the entry points

If you install directly the package use the entry point:

    oca-checks-odoo-module --help
    oca-checks-po --help


# Skip one xml-check for only one file

If you need to skip one check in one particular XML file you can use the follow comment

```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- oca-hooks:disable=xml-check-to-skip -->
<odoo>
...
</odoo>
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- oca-hooks:disable=xml-check-to-skip,
                       xml-check-to-skip2 -->
<odoo>
...
</odoo>
```

The position of the comment it is not relative to the line that throw the check

It disable the entire file

# Configuration
Behavior can be configured through several methods and as of now only consists of enabling/disabling checks.

## Enabling or Disabling Checks
Each available hook consists of multiple checks which can be enabled/disabled using any of the following methods (ordered by priority):

1. As an argument e.g., `oca-checks-odoo --enable=check-to-enable --disable=check-to-disable1,check-to-disable2`
2. Using environment variables `OCA_HOOKS_ENABLE` or `OCA_HOOKS_DISABLE` e.g., `export OCA_HOOKS_ENABLE=check1,check2`
3. A configuration file. The path to it can be specified with the argument `--config`. Alternatively a file named `.oca_hooks.cfg`
will be looked for (by default) in the following locations (in order):
   1. Current working directory
   2. Repo's root
   3. User's home

### Using a Configuration File
To enable or disable checks using a configuration file, add a `disable` or `enable` key under the `MESSAGES_CONTROL` section.
For example:
```
[MESSAGES_CONTROL]
enable=check-enable1,check-enable2
disable=check-to-disable
```

As stated before, each source has a certain priority. This means that if the environment variable `OCA_HOOKS_ENABLE=check1`
exists, the configuration file above would not have any effect when it comes to enabling checks, and the only enabled
check will be `check1`.

However, if `OCA_HOOKS_DISABLE` is not set, the configuration file will still have an effect and `check-to-disable` will
be disabled.

[//]: # (start-checks)

# Checks

* Check file-not-used
Check if there is a file created but not referenced from __manifest__.py

* Check manifest-syntax-error
Check if the manifest file has syntax error

* Check csv-duplicate-record-id
duplicate CSV "id" AKA xmlid but for CSV files

* Check csv-syntax-error
Check syntax error for CSV files declared in the manifest

* Check xml-deprecated-data-node
Deprecated <data> node inside <odoo> xml node

* Check xml-deprecated-oe-chatter

Odoo 18 introduced a new XML tag `<chatter/>` which replaces the old way to declare
chatters on form views. For more information, see:
https://github.com/odoo/odoo/pull/156463

* Check xml-deprecated-openerp-node
deprecated <openerp> xml node

* Check xml-deprecated-qweb-directive
for use of deprecated QWeb directives t-*-options

* Check xml-not-valid-char-link
The resource in in src/href contains a not valid character.

* Check xml-oe-structure-missing-id

Ensure all tags with class 'oe_structure' have an ID. For more information on the rationale, see:
https://github.com/OCA/odoo-pre-commit-hooks/issues/27

* Check xml-redundant-module-name

If the module is called "module_a" and the xmlid is
`<record id="module_a.xmlid_name1" ...`

The "module_a." is redundant it could be replaced to only
`<record id="xmlid_name1" ...`

* Check xml-dangerous-filter-wo-user
Check dangerous filter without a user assigned.

* Check xml-create-user-wo-reset-password
records of user without `context="{'no_reset_password': True}"`
This context avoid send email and mail log warning

* Check xml-view-dangerous-replace-low-priority in ir.ui.view

    <field name="priority" eval="10"/>
    ...
        <field name="name" position="replace"/>

* Check xml-deprecated-tree-attribute
  The tree-view declaration is using a deprecated attribute.

* Check xml-record-missing-id
Generated when a <record> tag has no id.

* Check xml-duplicate-record-id

If a module has duplicated record_id AKA xml_ids
file1.xml
    <record id="xmlid_name1"
file2.xml
    <record id="xmlid_name1"

* Check xml-duplicate-fields in all record nodes
    <record id="xmlid_name1"...
        <field name="field_name1"...
        <field name="field_name1"...

* Check xml-syntax-error
Check syntax of XML files declared in the Odoo manifest

* Check xml-dangerous-qweb-replace-low-priority
Dangerous qweb view defined with low priority

* Check xml-duplicate-template-id
Triggered when two templates share the same ID

* Check xml-xpath-translatable-item check `xpath` nodes using `contains(text(), 'Text translatable')`
Since that the text could be translated so it is a mutable value.
It could raise `ValueError` exception if the language is changed.


[//]: # (end-checks)


[//]: # (start-checks-po)

# Checks PO

* Check po-requires-module
Translation entry requires comment `#. module: MODULE`

* Check po-python-parse-printf
Check if `msgid` is using `str` variables like `%s`
So translation `msgstr` must be the same number of variables too

* Check po-python-parse-format
Check if `msgid` is using `str` variables like `{}`
So translation `msgstr` must be the same number of variables too

* Check po-duplicate-message-definition (message-id)
in all entries of PO files

* Check po-duplicate-model-definition
Verify that no entries share the same 'model:' tag

* Check po-pretty-format
Check the following:
1. Entries sorted alphabetically
2. Lines are wrapped at 78 columns (same as Odoo)
3. Clear msgstr when it is the same as msgid

* Check po-syntax-error
Check syntax of PO files from i18n* folders


[//]: # (end-checks-po)


[//]: # (start-help)

# Help
```bash
usage: oca-checks-odoo-module [-h] [--no-verbose] [--no-exit] [--disable DISABLE] [--enable ENABLE] [--config CONFIG] [--list-msgs] [--fix] [files_or_modules ...]

positional arguments:
 files_or_modules Odoo __manifest__.py paths or Odoo module paths.

options:
 -h, --help show this help message and exit
 --no-verbose If enabled so disable verbose mode.
 --no-exit If enabled so it will not call exit.
 --disable, -d DISABLE Disable the checker with the given 'check-name', separated by commas.
 --enable, -e ENABLE Enable the checker with the given 'check-name', separated by commas. Default: All checks are enabled by default
 --config, -c CONFIG Path to a configuration file (default: .oca_hooks.cfg)
 --list-msgs List all currently enabled messages.
 --fix Automatically fix files when possible

```

[//]: # (end-help)


[//]: # (start-help-po)

# Help PO
```bash
usage: oca-checks-po [-h] [--no-verbose] [--no-exit] [--disable DISABLE] [--enable ENABLE] [--config CONFIG] [--list-msgs] [--fix] [po_files ...]

positional arguments:
 po_files PO files.

options:
 -h, --help show this help message and exit
 --no-verbose If enabled so disable verbose mode.
 --no-exit If enabled so it will not call exit.
 --disable, -d DISABLE Disable the checker with the given 'check-name', separated by commas.
 --enable, -e ENABLE Enable the checker with the given 'check-name', separated by commas. Default: All checks are enabled by default
 --config, -c CONFIG Path to a configuration file (default: .oca_hooks.cfg)
 --list-msgs List all currently enabled messages.
 --fix Automatically fix files when possible

```

[//]: # (end-help-po)


[//]: # (start-example)

# Examples


 * csv-duplicate-record-id

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/ir.model.access.csv#L5 Duplicate CSV record `access_account_account_type`

 * csv-syntax-error

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/syntax_err_module/ir.model.access.csv#L1 'utf-8' codec can't decode byte 0xf1 in position 47: invalid continuation byte

 * file-not-used

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/__openerp__.py#L1 File "broken_module/report/test_report.xml" is not referenced in the manifest.

 * manifest-syntax-error

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/manifest_werror/__manifest__.py#L1 Manifest could not be loaded
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/woinit_module/__manifest__.py#L1 Manifest could not be loaded

 * xml-create-user-wo-reset-password

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/res_users.xml#L10 record res.users without `context="{'no_reset_password': True}"`

 * xml-dangerous-filter-wo-user

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view_odoo.xml#L60 Dangerous filter without explicit `user_id`

 * xml-dangerous-qweb-replace-low-priority

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1.xml#L4 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1.xml#L7 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1.xml#L18 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1_copy.xml#L4 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1_copy.xml#L7 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1_copy.xml#L18 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1_copy2.xml#L4 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1_copy2.xml#L7 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1_copy2.xml#L18 Dangerous use of `replace` from view with priority 0 < 99

 * xml-deprecated-data-node

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/demo/duplicated_id_demo.xml#L3 Deprecated `<data>` node
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view.xml#L3 Deprecated `<data>` node
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view2.xml#L3 Deprecated `<data>` node
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view_odoo.xml#L3 Deprecated `<data>` node
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view_odoo2.xml#L3 Deprecated `<data>` node
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/skip_xml_check.xml#L5 Deprecated `<data>` node
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/skip_xml_check_2.xml#L3 Deprecated `<data>` node
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/model_view.xml#L3 Deprecated `<data>` node

 * xml-deprecated-oe-chatter

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/odoo18_module/views/deprecated_chatter.xml#L6 Please replace old style chatters with the new tag <chatter/>.

 * xml-deprecated-openerp-node

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view.xml#L2 Deprecated `<openerp>` xml node
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view2.xml#L2 Deprecated `<openerp>` xml node
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/model_view.xml#L2 Deprecated `<openerp>` xml node
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/res_users.xml#L2 Deprecated `<openerp>` xml node

 * xml-deprecated-qweb-directive

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L7 Deprecated QWeb directive `t-esc-options`. Use `t-options` instead
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L20 Deprecated QWeb directive `t-field-options`. Use `t-options` instead

 * xml-deprecated-tree-attribute

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view_odoo.xml#L31 Deprecated "<tree string=..."
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view_odoo.xml#L42 Deprecated "<tree colors=..."
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view_odoo.xml#L53 Deprecated "<tree fonts=..."

 * xml-duplicate-fields

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view_odoo2.xml#L6 Duplicate xml field `name`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view_odoo2.xml#L19 Duplicate xml field `model`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view_odoo2.xml#L74 Duplicate xml field `arch`

 * xml-duplicate-record-id

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view.xml#L5 Duplicate xml record id `view_model_form`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view2.xml#L5 Duplicate xml record id `view_model_form2`

 * xml-duplicate-template-id

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1.xml#L3 Duplicate xml template id `qweb/my_template1_noupdate_0`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1.xml#L10 Duplicate xml template id `qweb/my_template2_noupdate_0`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1.xml#L17 Duplicate xml template id `qweb/my_template3_noupdate_0`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1.xml#L21 Duplicate xml template id `qweb/my_template4_noupdate_0`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1.xml#L25 Duplicate xml template id `qweb/my_template5_noupdate_0`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1.xml#L29 Duplicate xml template id `qweb/my_duplicate_template_noupdate_0`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L5 Duplicate xml template id `data/test_template_1_noupdate_0`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L18 Duplicate xml template id `data/test_template_2_noupdate_0`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L36 Duplicate xml template id `data/assets_backend_noupdate_0`

 * xml-not-valid-char-link

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L39 The resource in in src/href contains a not valid character
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L41 The resource in in src/href contains a not valid character

 * xml-oe-structure-missing-id

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L9 Consider removing the class `oe_structure` or adding a proper id to the tag. The id must contain `oe_structure`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L13 Consider removing the class `oe_structure` or adding a proper id to the tag. The id must contain `oe_structure`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L24 Consider removing the class `oe_structure` or adding a proper id to the tag. The id must contain `oe_structure`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L27 Consider removing the class `oe_structure` or adding a proper id to the tag. The id must contain `oe_structure`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates.xml#L30 Consider removing the class `oe_structure` or adding a proper id to the tag. The id must contain `oe_structure`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/website_templates_disable.xml#L21 Consider removing the class `oe_structure` or adding a proper id to the tag. The id must contain `oe_structure`

 * xml-record-missing-id

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view.xml#L25 Record has no id, add a unique one to create a new record, use an existing one to update it
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view.xml#L28 Record has no id, add a unique one to create a new record, use an existing one to update it

 * xml-redundant-module-name

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view2.xml#L15 Redundant module name `<record id="broken_module.view_model_form2" />`

 * xml-syntax-error

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/file_no_exist.xml#L1 [Errno 2] No such file or directory: ''
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/file_no_exist.xml#L1 [Errno 2] No such file or directory: ''

 * xml-view-dangerous-replace-low-priority

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view2.xml#L25 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view2.xml#L37 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view2.xml#L47 Dangerous use of `replace` from view with priority 0 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view2.xml#L70 Dangerous use of `replace` from view with priority 10 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view2.xml#L92 Dangerous use of `replace` from view with priority 10 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view2.xml#L108 Dangerous use of `replace` from view with priority 10 < 99
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/skip_xml_check_3.xml#L15 Dangerous use of `replace` from view with priority 0 < 99

 * xml-xpath-translatable-item

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/model_view.xml#L11 Use of translatable xpath `text()`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1.xml#L39 Use of translatable xpath `text()`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1_copy.xml#L31 Use of translatable xpath `text()`
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/template1_copy2.xml#L31 Use of translatable xpath `text()`

[//]: # (end-example)


[//]: # (start-example-po)

# Examples PO


 * po-duplicate-message-definition

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L17 Duplicate PO message definition `Branch` in lines 23, 29. Odoo exports these items by msgid and delete one of them. Use the `i18n_extra` folder instead of `i18n` to ignore this message.
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L35 Duplicate PO message definition `Message id toooooooooooooooooooooooooooo...` in lines 41. Odoo exports these items by msgid and delete one of them. Use the `i18n_extra` folder instead of `i18n` to ignore this message.
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L65 Duplicate PO message definition `One variable {variable1}` in lines 71. Odoo exports these items by msgid and delete one of them. Use the `i18n_extra` folder instead of `i18n` to ignore this message.

 * po-duplicate-model-definition

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L17 Translation for model:ir.model.fields,field_description:broken_module.field_wizard_description has been defined more than once in line(s) 29
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L35 Translation for model:ir.model.fields,field_description2:broken_module.field_wizard_description2 has been defined more than once in line(s) 41
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L59 Translation for model:ir.model.fields,field_description5:broken_module.field_wizard_description5 has been defined more than once in line(s) 65
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/i18n/fr.po#L24 Translation for model:ir.model.fields,field_description2:test_module.field_description2 has been defined more than once in line(s) 24
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/i18n/fr.po#L31 Translation for model:ir.model.fields,field_description5:test_module.field_description5 has been defined more than once in line(s) 31
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/i18n/fr.po#L38 Translation for model:ir.model.fields,field_description3:test_module.field_description3 has been defined more than once in line(s) 38
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/i18n/fr.po#L45 Translation for model:ir.model.fields,field_description4:test_module.field_description4 has been defined more than once in line(s) 45

 * po-pretty-format

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/ar_unicode.po Wrong formatting
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/broken_module.pot Wrong formatting
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po Wrong formatting
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/xml_semi_empty.po Wrong formatting
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/eleven_module/i18n/ugly.po Wrong formatting
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/test_module/i18n/fr.po Wrong formatting

 * po-python-parse-format

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L53 Translation string couldn't be parsed correctly using str.format IndexError('Replacement index 1 out of range for positional args tuple')
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L59 Translation string couldn't be parsed correctly using str.format IndexError('Replacement index 1 out of range for positional args tuple')
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L65 Translation string couldn't be parsed correctly using str.format KeyError('variable2')
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L71 Translation string couldn't be parsed correctly using str.format KeyError('variable2')

 * po-python-parse-printf

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L47 Translation string couldn't be parsed correctly using str%variables TypeError('not all arguments converted during string formatting')
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/es.po#L83 Translation string couldn't be parsed correctly using str%variables TypeError('%d format: a real number is required, not str')

 * po-requires-module

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module/i18n/broken_module.pot#L14 Translation entry requires comment `#. module: MODULE`

 * po-syntax-error

    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/broken_module2/i18n/en.po#L1 Syntax error in po file
    - https://github.com/OCA/odoo-pre-commit-hooks/blob/v0.1.6/test_repo/syntax_err_module/i18n/es.po#L19 Syntax error in po file

[//]: # (end-example-po)

## Licenses

This repository is licensed under [AGPL-3.0](LICENSE).

----
OCA, or the [Odoo Community Association](http://odoo-community.org/), is a nonprofit
organization whose mission is to support the collaborative development of Odoo features
and promote its widespread use.
