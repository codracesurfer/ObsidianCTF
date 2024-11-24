### Level19

break on interpret_cmp and check the compare `if ( v5 < v6 )` these need to be equal

```c
__int64 __fastcall interpret_cmp(__int64 a1, __int16 a2)
{
  const char *v2; // rbx
  const char *v3; // rax
  __int64 result; // rax
  unsigned __int8 v5; // [rsp+1Eh] [rbp-12h]
  unsigned __int8 v6; // [rsp+1Fh] [rbp-11h]

  v2 = (const char *)describe_register((unsigned __int8)a2);
  v3 = (const char *)describe_register(HIBYTE(a2));
  printf("[s] CMP %s %s\n", v3, v2);
  v5 = read_register(a1, HIBYTE(a2));
  v6 = read_register(a1, (unsigned __int8)a2);
  *(_BYTE *)(a1 + 1030) = 0;
  if ( v5 < v6 )
    *(_BYTE *)(a1 + 1030) |= 8u;
  if ( v5 > v6 )
    *(_BYTE *)(a1 + 1030) |= 2u;
  if ( v5 == v6 )
    *(_BYTE *)(a1 + 1030) |= 0x10u;
  result = v5;
  if ( v5 != v6 )
  {
    result = a1;
    *(_BYTE *)(a1 + 1030) |= 4u;
  }
  if ( !v5 && !v6 )
  {
    result = a1;
    *(_BYTE *)(a1 + 1030) |= 1u;
  }
  return result;
}
```

