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

`[in] ProcessHandle`

Identificador del proceso para el que se debe realizar la asignación. Use la macro NtCurrentProcess , definida en Ntddk.h, para especificar el proceso actual.

`[in, out] BaseAddress`

Puntero a una variable que recibirá la dirección base de la región asignada de páginas. Si el valor inicial de BaseAddress no es NULL, la región se asigna a partir de la dirección virtual especificada redondeada hacia abajo hasta el siguiente límite de dirección de tamaño de página host. Si el valor inicial de BaseAddress es NULL, el sistema operativo determinará dónde asignar la región.

`[in] ZeroBits`

Número de bits de dirección de orden superior que deben ser cero en la dirección base de la vista de sección. Se usa solo cuando el sistema operativo determina dónde asignar la región, como cuando BaseAddress\* es NULL. Tenga en cuenta que cuando ZeroBits es mayor que 32, se convierte en una máscara de bits.

`[in, out] RegionSize`

Puntero a una variable que recibirá el tamaño real, en bytes, de la región asignada de páginas. El valor inicial de RegionSize especifica el tamaño, en bytes, de la región y se redondea hasta el siguiente límite de tamaño de página host. RegionSize no puede ser cero en la entrada.

`[in] AllocationType`

Máscara de bits que contiene marcas que especifican el tipo de asignación que se va a realizar para la región de páginas especificada. En la tabla siguiente se describen las marcas más comunes. Consulte VirtualAlloc para obtener una lista completa de las posibles marcas y descripciones.

<pre style="background-color: lavender; padding: 10px; border-radius: 5px;">
Nota
Se debe establecer uno de MEM_COMMIT, MEM_RESET o MEM_RESERVE.
</pre>

|                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| MEM_COMMIT             | La región especificada de páginas se va a confirmar.                                                                                                                                                                                                                                                                                                                                                                                                                  |
| MEM_RESERVE            | La región especificada de las páginas se va a reservar.                                                                                                                                                                                                                                                                                                                                                                                                               |
| MEM_RESET              | Restablezca el estado de la región especificada para que, si las páginas están en el archivo de paginación, se descartan y se traen páginas de ceros. Si las páginas están en memoria y modificadas, se marcan como no modificadas para que no se escriban en el archivo de paginación. El contenido no está a cero. El parámetro Protect no se usa, pero debe establecerse en un valor válido. Si se establece MEM_RESET, no se puede establecer ninguna otra marca. |
| Otras marcas (MEM_XXX) | Consulte [VirtualAlloc](https://learn.microsoft.com/es-es/windows-hardware/drivers/ddi/ntifs/nf-ntifs-ntallocatevirtualmemory)                                                                                                                                                                                                                                                                                                                                        |

`[in] Protect`
Máscara de bits que contiene marcas de protección de páginas que especifican la protección deseada para la región confirmada de páginas. En la tabla siguiente se describen estas marcas.

|                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PAGE_NOACCESS     | No se permite el acceso a la región confirmada de páginas. Un intento de leer, escribir o ejecutar la región confirmada produce una excepción de infracción de acceso, denominada error de protección general (GP).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| PAGE_READONLY     | Se permite el acceso de solo lectura y ejecución a la región confirmada de páginas. Un intento de escribir la región confirmada produce una infracción de acceso.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| PAGE_READWRITE    | Se permite el acceso de lectura, escritura y ejecución a la región confirmada de páginas. Si se permite el acceso de escritura a la sección subyacente, se comparte una sola copia de las páginas. De lo contrario, las páginas se comparten de solo lectura o copia en escritura.                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| PAGE_EXECUTE      | Se permite ejecutar el acceso a la región confirmada de páginas. Un intento de leer o escribir en la región confirmada produce una infracción de acceso.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| PAGE_EXECUTE_READ | Se permite el acceso de ejecución y lectura a la región confirmada de páginas. Un intento de escribir en la región confirmada produce una infracción de acceso.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| PAGE_GUARD        | Las páginas de la región se convierten en páginas de protección. Cualquier intento de lectura o escritura en una página de protección hace que el sistema genere una excepción de STATUS_GUARD_PAGE. Las páginas de protección actúan como una alarma de acceso único. Esta marca es un modificador de protección de páginas, válido solo cuando se usa con una de las marcas de protección de páginas distintas de PAGE_NOACCESS. Cuando un intento de acceso lleva al sistema a desactivar el estado de la página de protección, la protección de página subyacente se hace cargo. Si se produce una excepción de página de protección durante un servicio del sistema, el servicio normalmente devuelve un indicador de estado de error. |
| PAGE_NOCACHE      | La región de las páginas debe asignarse como noquecheable. no se permite PAGE_NOCACHE para las secciones.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| PAGE_WRITECOMBINE | Permite la combinación de escritura, es decir, la fusión de escrituras de la memoria caché a la memoria principal, donde el hardware lo admite. Esta marca se usa principalmente para la memoria del búfer de fotogramas para que las escrituras en la misma línea de caché se combinen siempre que sea posible antes de escribirse en el dispositivo. Esto puede reducir considerablemente las escrituras en el bus a la memoria de vídeo (por ejemplo). Si el hardware no admite la combinación de escritura, se omite la marca. Esta marca es un modificador de protección de páginas, válido solo cuando se usa con una de las marcas de protección de páginas distintas de PAGE_NOACCESS.                                              |

## Valor devuelto

**NtAllocateVirtualMemory** devuelve STATUS_SUCCESS o un código de estado de error. Entre los posibles códigos de estado de error se incluyen los siguientes:

- **STATUS_ACCESS_DENIED**
- **STATUS_ALREADY_COMMITTED**
- **STATUS_COMMITMENT_LIMIT**
- **STATUS_CONFLICTING_ADDRESSES**
- **STATUS_INSUFFICIENT_RESOURCES**
- **STATUS_INVALID_HANDLE**
- **STATUS_INVALID_PAGE_PROTECTION**
- **STATUS_NO_MEMORY**
- **STATUS_OBJECT_TYPE_MISMATCH**
- **STATUS_PROCESS_IS_TERMINATING**

## Comentarios

NtAllocateVirtualMemory puede realizar las siguientes operaciones:

- Confirme una región de páginas reservadas por una llamada anterior a NtAllocateVirtualMemory.
- Reserva una región de páginas gratuitas.
- Reserve y confirme una región de páginas gratuitas.
  Los controladores en modo kernel pueden usar NtAllocateVirtualMemory para reservar un intervalo de direcciones virtuales accesibles para la aplicación en el proceso especificado y, a continuación, realizar llamadas adicionales a NtAllocateVirtualMemory para confirmar páginas individuales desde el intervalo reservado. Esto permite que un proceso reserve un intervalo de su espacio de direcciones virtuales sin consumir almacenamiento físico hasta que sea necesario.

Cada página del espacio de direcciones virtuales del proceso se encuentra en uno de los tres estados descritos en la tabla siguiente.

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
