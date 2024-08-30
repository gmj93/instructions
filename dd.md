# High compression

```
 dd bs=1M iflag=fullblock if=<input_dev> status=progress | zstd -16v > <output_img.zst>
 zstdcat <output_img.zst> | dd bs=1M iflag=fullblock of=<input_dev> status=progress 
```

# Basic compression and 4 threads

```
 dd bs=1M iflag=fullblock if=<input_dev> status=progress | zstd -2T4v > <output_img.zst>
 zstdcat <output_img.zst> | dd bs=1M iflag=fullblock of=<input_dev> status=progress 
```
