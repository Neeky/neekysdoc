## 概要

安装 mysqlclient 报错

```bash
pip3 install mysqlclient
Looking in indexes: https://mirrors.cloud.tencent.com/pypi/simple
Collecting mysqlclient
  Using cached https://mirrors.cloud.tencent.com/pypi/packages/79/33/996dc0ba3f03e2399adc91a7de1f61cb14b57ebdb4cc6eca8a78723043cb/mysqlclient-2.2.4.tar.gz (90 kB)
  Installing build dependencies ... done
  Getting requirements to build wheel ... error
  error: subprocess-exited-with-error
  
  × Getting requirements to build wheel did not run successfully.
  │ exit code: 1
  ╰─> [27 lines of output]
      Trying pkg-config --exists mysqlclient
      Command 'pkg-config --exists mysqlclient' returned non-zero exit status 1.
      Trying pkg-config --exists mariadb
      Command 'pkg-config --exists mariadb' returned non-zero exit status 1.
      Trying pkg-config --exists libmariadb
      Command 'pkg-config --exists libmariadb' returned non-zero exit status 1.
      Traceback (most recent call last):
        File "/usr/local/python-3.12.3/lib/python3.12/site-packages/pip/_vendor/pyproject_hooks/_in_process/_in_process.py", line 353, in <module>
          main()
        File "/usr/local/python-3.12.3/lib/python3.12/site-packages/pip/_vendor/pyproject_hooks/_in_process/_in_process.py", line 335, in main
          json_out['return_val'] = hook(**hook_input['kwargs'])
                                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        File "/usr/local/python-3.12.3/lib/python3.12/site-packages/pip/_vendor/pyproject_hooks/_in_process/_in_process.py", line 118, in get_requires_for_build_wheel
          return hook(config_settings)
                 ^^^^^^^^^^^^^^^^^^^^^
        File "/tmp/pip-build-env-_hh7rsde/overlay/lib/python3.12/site-packages/setuptools/build_meta.py", line 325, in get_requires_for_build_wheel
          return self._get_build_requires(config_settings, requirements=['wheel'])
                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        File "/tmp/pip-build-env-_hh7rsde/overlay/lib/python3.12/site-packages/setuptools/build_meta.py", line 295, in _get_build_requires
          self.run_setup()
        File "/tmp/pip-build-env-_hh7rsde/overlay/lib/python3.12/site-packages/setuptools/build_meta.py", line 311, in run_setup
          exec(code, locals())
        File "<string>", line 155, in <module>
        File "<string>", line 49, in get_config_posix
        File "<string>", line 28, in find_package_name
      Exception: Can not find valid pkg-config name.
      Specify MYSQLCLIENT_CFLAGS and MYSQLCLIENT_LDFLAGS env vars manually
      [end of output]
  
  note: This error originates from a subprocess, and is likely not a problem with pip.
error: subprocess-exited-with-error

× Getting requirements to build wheel did not run successfully.
│ exit code: 1
╰─> See above for output.

note: This error originates from a subprocess, and is likely not a problem with pip.
```

---

## 解决办法

```bash
PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/mysql-8.4.0-linux-glibc2.28-x86_64/lib/pkgconfig
export PKG_CONFIG_PATH
```

---

## 安装

```bash
pip3 install mysqlclient
Looking in indexes: https://mirrors.cloud.tencent.com/pypi/simple
Collecting mysqlclient
  Using cached https://mirrors.cloud.tencent.com/pypi/packages/79/33/996dc0ba3f03e2399adc91a7de1f61cb14b57ebdb4cc6eca8a78723043cb/mysqlclient-2.2.4.tar.gz (90 kB)
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
  Installing backend dependencies ... done
  Preparing metadata (pyproject.toml) ... done
Building wheels for collected packages: mysqlclient
  Building wheel for mysqlclient (pyproject.toml) ... done
  Created wheel for mysqlclient: filename=mysqlclient-2.2.4-cp312-cp312-linux_x86_64.whl size=131165 sha256=9fb32302921dfcd9c89f62154e94fb76a9d6085be10945eb2be9ff56396ced48
  Stored in directory: /root/.cache/pip/wheels/3f/e4/2b/86804b9ef19a38c6ef01e5aa6ddde1a0ab4f64c4ebc6773df6
Successfully built mysqlclient
Installing collected packages: mysqlclient
Successfully installed mysqlclient-2.2.4
```

---