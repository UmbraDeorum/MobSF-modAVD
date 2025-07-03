Modifications tracker:

## General

* updated apt package lists
* installed nano for editing

- commited the modified version locally, and saved it in MobSF-modAVD-vX.X.tar format, to be `docker load`ed when in a rush.
- `7z`ipped the MobSF-modAVD-vX.X.tar archive, for portability.

## environment.py

* removed all `adb connect <self.identifier>` calls.
* replaced all instances of adb being called with the '-s' flag and the self.identifier parameter, with straight adb calls;<br/>
  
  **Example 1**
  
  in `adb_command`:
    ```python
    args = [get_adb(),
                    '-s',
                    self.identifier]
    ```
    was replaced with:
    ```python
    args = [get_adb()]
    ```
  
  **Example 2**
  
  in `system_check`:
    ```python
  proc = subprocess.Popen([get_adb(),
                                       '-s', self.identifier,
                                       'shell',
                                       'touch',
                                       '/system/test'],
    ```
    was replaced with:
    ```python
  proc = subprocess.Popen([get_adb(),
                                       'shell',
                                       'touch',
                                       '/system/test'],
    ```

* The following part of the `__init__` function:
  ```python
  if identifier:
      self.identifier = identifier
  else:
      self.identifier = get_device()
  ```
  was replaced with:
  ```python
  self.identifier = ''
  ```

* All `if not self.identifier` checks have been removed, along with their return values.

## frida_core.py

Calls which were bound to raise exceptions due to the changes in **environment.py** were modified.
all cases of:
```python
device = frida.get_device(
                get_device(),
                settings.FRIDA_TIMEOUT)
```

were replaced with:
```python
device = frida.get_usb_device()
```

## utils.py

Added a line after:
```python
logger.info('Author: Ajin Abraham | opensecurity.in')    
```
to introduce some self-validation (^_^):
```python
logger.info('Author: Ajin Abraham | opensecurity.in')
logger.info('Modded by: Umbr4Dε0rum | twelvesec.com')
```
