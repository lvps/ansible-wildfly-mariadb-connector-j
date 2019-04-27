Role Name
=========

Install MariaDB Connector/J as a WildFly module.

How it works
------------

MariaDB Connector/J is not available in any repository for CentOS as far as I can tell, so this role downloads Connector/J JAR and drops it in the specified directory. It also adds a `module.xml` to tell WildFly that there's a module.

The specified directory should end with `.../org/mariadb/jdbc/main/` since that part is hardcoded in the module.xml and possibly in the JAR.

If you want to automatically delete old JARs when a new version is released and downloaded, change set `wildfly_mariadb_connector_j_clean_old_files` to true: it will search for any file in the specified directory that matches `mariadb-java-client-*.jar`, except the one that it just downloaded, and deletes them. If you only have this Ansible role managing that directory, this is probably a safe operation.

By default, 'latest' version is downloaded: this means that a few tasks determine the version number of the latest version, e.g. 2.3.0, and download that JAR.

The directory is created if it doesn't exist and files are placed there, but you'll need to set file ownership, permissions and SELinux stuff in a separate task if you need that.

This role also registers the `wildfly_mariadb_connector_j_changed` variable: you can use it to detect when Connector/J has been updated and restart your application, e.g:

```yaml
- name: Restart your application
  shell: "..."
  when: wildfly_mariadb_connector_j_changed.changed
```

(not the double "changed")

Requirements
------------

Only tested only on CentOS 7, but may work on other platforms, too.

Role Variables
--------------

* `wildfly_mariadb_connector_j_version`: 'latest' or a version number. Default 'latest'.
* `wildfly_mariadb_connector_j_directory`: where to place the jar and module.xml
* `wildfly_mariadb_connector_j_clean_old_files`: delete old JARs when a new version (with a different version number) is downloaded. Default false.

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- hosts: servers
  roles:
    - {
      role: ansible-wildfly-mariadb-connector-j,
      wildfly_mariadb_connector_j_version: 'latest',
      wildfly_mariadb_connector_j_path: '/opt/foo/modules/system/layers/foo/org/mariadb/jdbc/main/'
      wildfly_mariadb_connector_j_clean_old_files: true
	}
```

License
-------

MIT
