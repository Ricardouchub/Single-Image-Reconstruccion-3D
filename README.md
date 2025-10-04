
# Reconstrucción 3D monocular en ModelNet40 con PyTorch (PSGN)

Resumen
-------
Este proyecto toma una imagen RGB de un objeto y genera una reconstrucción 3D como nube de puntos (N×3).
Implementa un baseline tipo PSGN (Point Set Generation Network): ResNet-18 como encoder y un MLP como
decodificador de puntos. Se entrenó y evaluó en ModelNet40, guardando artefactos de prueba (PNG/PLY/CSV)
en la carpeta `tests/`.

Resultados (ejemplo de una corrida)
-----------------------------------
- Chamfer Distance: 0.034009
- F-score@1%: 0.447 (P=0.478, R=0.420)

Arquitectura
------------
- Encoder: ResNet-18 (pre-entrenado en ImageNet).
- Decoder: MLP → N×3 puntos (nube de puntos).
- Pérdidas: Chamfer Distance (principal). Opcional: Repulsion Loss y Silhouette Loss.
- Export: `.ply` para inspección con Open3D/MeshLab; remallado opcional a `.obj/.glb` (Poisson/BPA).

Datos
-----
- Dataset: ModelNet40 (Princeton). Los `.off` se normalizan a unidad y se muestrea la superficie a N=2048 puntos.
- Entrada: render 2D on-the-fly con PyTorch3D (o pre-render a disco para acelerar).
- Salida: nube de puntos 3D alineada en [-1,1]^3 aprox.

Requisitos (mi entorno)
-----------------------
- Windows 10/11 con GPU NVIDIA (RTX 3060, 12 GB VRAM).
- Python 3.11
- PyTorch 2.6.0 + CUDA 12.4, TorchVision 0.21.x
- (Opcional para render) PyTorch3D construido desde fuente (CUDA Toolkit 12.4 + MSVC Build Tools).
- Librerías: torchvision, open3d, trimesh, matplotlib, numpy.

Uso rápido (notebooks)
----------------------
1) Abrir **Anaconda Prompt** y activar el entorno, luego cargar MSVC (si usarás PyTorch3D):
   ```bat
   conda activate nlp311
   CALL "C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build\vcvars64.bat"
   ```
2) Lanzar Jupyter y abrir el notebook principal del proyecto.
3) Entrenar el baseline PSGN (imagen → nube de puntos) con Chamfer.
4) Ejecutar el harness de **tests**: guarda en `tests/` los PNG (input|pred|GT), PLY (pred/gt), y `results.csv` con métricas.
5) (Opcional) Exportar `.obj/.glb` mediante remallado (Poisson o Ball-Pivoting).

Evaluación
----------
- Chamfer Distance (CD): distancia media de punto a superficie (simétrica).
- F-score@τ: con umbral τ relativo al tamaño del objeto (típicamente τ=1% del diámetro).
- Se reportan promedios y ejemplos cualitativos. En `tests/` se almacenan triptychs (input|pred|gt) y PLYs.

Limitaciones
------------
- Ambigüedad inherente de single-view (zonas ocultas). Detalle fino limitado al ser nube de puntos.
- Brecha de dominio entre renders y fotos reales.
- PyTorch3D en Windows requiere toolchain de compilación; como alternativa, pre-render en disco.

Trabajo futuro
--------------
- Añadir Silhouette Loss estable (render diferenciable) y/o Multi-View Consistency.
- Probar AtlasNet/patch-based decoders y upsampling de puntos.
- Entrenamiento con imágenes reales + adaptación de dominio.
- Métricas por clase y tabla comparativa con ablations (N puntos, tamaño de imagen, etc.).


Cita / Créditos
---------------
- PSGN: Fan, Su, Guibas (CVPR 2017). Implementación simplificada en PyTorch.
- ModelNet40: Wu et al. (Princeton ModelNet).

(Generado: 2025-10-04 15:07:04)
