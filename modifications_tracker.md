Modifications tracker:

## General

* updated apt package lists
* installed nano for editing

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
  
