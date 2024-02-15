# NtAllocateVirtualMemory

---

### [NtAllocateVirtualMemory Microsoft DOC](https://learn.microsoft.com/es-es/windows-hardware/drivers/ddi/ntifs/nf-ntifs-ntallocatevirtualmemory)


La rutina NtAllocateVirtualMemory reserva, confirma o ambas, una región de páginas dentro del espacio de direcciones virtuales en modo de usuario de un proceso especificado.

---

**Sintaxis C++**
```cpp
__kernel_entry NTSYSCALLAPI NTSTATUS NtAllocateVirtualMemory(
  [in]      HANDLE    ProcessHandle,
  [in, out] PVOID     *BaseAddress,
  [in]      ULONG_PTR ZeroBits,
  [in, out] PSIZE_T   RegionSize,
  [in]      ULONG     AllocationType,
  [in]      ULONG     Protect
);
```

---

### Parámetros
```[in] ProcessHandle```

Identificador del proceso para el que se debe realizar la asignación. Use la macro NtCurrentProcess , definida en Ntddk.h, para especificar el proceso actual.

```[in, out] BaseAddress```

Puntero a una variable que recibirá la dirección base de la región asignada de páginas. Si el valor inicial de BaseAddress no es NULL, la región se asigna a partir de la dirección virtual especificada redondeada hacia abajo hasta el siguiente límite de dirección de tamaño de página host. Si el valor inicial de BaseAddress es NULL, el sistema operativo determinará dónde asignar la región.

```[in] ZeroBits```

Número de bits de dirección de orden superior que deben ser cero en la dirección base de la vista de sección. Se usa solo cuando el sistema operativo determina dónde asignar la región, como cuando BaseAddress* es NULL. Tenga en cuenta que cuando ZeroBits es mayor que 32, se convierte en una máscara de bits.

```[in, out] RegionSize```

Puntero a una variable que recibirá el tamaño real, en bytes, de la región asignada de páginas. El valor inicial de RegionSize especifica el tamaño, en bytes, de la región y se redondea hasta el siguiente límite de tamaño de página host. RegionSize no puede ser cero en la entrada.

```[in] AllocationType```

Máscara de bits que contiene marcas que especifican el tipo de asignación que se va a realizar para la región de páginas especificada. En la tabla siguiente se describen las marcas más comunes. Consulte VirtualAlloc para obtener una lista completa de las posibles marcas y descripciones.


<pre style="background-color: lavender; padding: 10px; border-radius: 5px;">
Nota
Se debe establecer uno de MEM_COMMIT, MEM_RESET o MEM_RESERVE.
</pre>


|                                   |                                                                  |
| :-------------------------------- | ---------------------------------------------------------------- |
| **Cliente mínimo compatible**     | Windows 2000                                                     |
| **Plataforma de destino**         | Universal                                                        |
| **Encabezado**                    | ntifs.h (incluya Ntifs.h)                                        |
| **Library**                       | NtosKrnl.lib                                                     |
| **Archivo DLL**                   | NtosKrnl.exe                                                     |
| **IRQL**                          | PASSIVE_LEVEL                                                    |
| **Reglas de cumplimiento de DDI** | HwStorPortProhibitedDIs, PowerIrpDDis, SpNoWait, StorPortStartIo |

---
