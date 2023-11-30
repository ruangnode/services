# Basic command LVM

Logical Volume Management (LVM) is a powerful disk management tool in Linux that allows you to dynamically manage disk partitions and volumes. Here are some basic LVM commands:

1. **Physical Volume (PV) Commands**:
   * To create a physical volume: `pvcreate /dev/sdX` (Replace `/dev/sdX` with the actual device name.)
   * To display information about physical volumes: `pvdisplay`
   * To remove a physical volume from LVM: `pvremove /dev/sdX`
2. **Volume Group (VG) Commands**:
   * To create a volume group: `vgcreate <vg_name> /dev/sdX` (Replace `<vg_name>` with your chosen name and `/dev/sdX` with the PV.)
   * To display information about volume groups: `vgdisplay`
   * To extend a volume group: `vgextend <vg_name> /dev/sdY` (Add another PV to the existing VG.)
   * To reduce a volume group: `vgreduce <vg_name> /dev/sdZ` (Remove a PV from the VG.)
   * To remove a volume group: `vgremove <vg_name>`
3.  **Logical Volume (LV) Commands**:

    * To create a logical volume within a VG: `lvcreate -L <size> -n <lv_name> <vg_name>` (Replace `<size>` with the size, `<lv_name>` with the LV name, and `<vg_name>` with the VG name.)
    * To display information about logical volumes: `lvdisplay`
    * To resize a logical volume: `lvresize -L +<new_size> /dev/<vg_name>/<lv_name>` (Increase the LV size.)
    * To remove a logical volume: `lvremove /dev/<vg_name>/<lv_name>`



    `lvextend` and `lvresize` are both commands used in Logical Volume Management (LVM) to change the size of a logical volume (LV), but they serve slightly different purposes:

    1. **`lvextend`**:
       * **Purpose**: The `lvextend` command is used to increase the size of a logical volume by adding more physical extents from the associated volume group (VG).
       * **Usage Example**: You would use `lvextend` when you want to make an LV larger by adding available space in the VG to it. For instance, when you add a new physical disk to the VG and want to use that space to expand an existing LV.
       * **Command**: `lvextend -l +100%FREE /dev/<vg_name>/<lv_name>` (This adds all available space from the VG to the LV.)
    2. **`lvresize`**:
       * **Purpose**: The `lvresize` command is used to change the size of a logical volume, either by shrinking or expanding it. It allows you to specify the new size explicitly, which can be larger or smaller than the current size.
       * **Usage Example**: You would use `lvresize` when you want to make precise changes to the LV's size, such as shrinking it to free up space in the VG or expanding it to a specific size.
       * **Command**: `lvresize -L +<size> /dev/<vg_name>/<lv_name>` (To increase the LV size by `<size>`.) OR `lvresize -L -<size> /dev/<vg_name>/<lv_name>` (To decrease the LV size by `<size>`.)
4. **Other LVM Commands**:
   * To activate all VGs: `vgchange -ay`
   * To deactivate a VG: `vgchange -an <vg_name>`
   * To activate an LV: `lvchange -ay /dev/<vg_name>/<lv_name>`
   * To deactivate an LV: `lvchange -an /dev/<vg_name>/<lv_name>`
