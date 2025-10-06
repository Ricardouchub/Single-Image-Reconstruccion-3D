
# Reconstrucción 3D monocular en ModelNet40 con PyTorch (PSGN)

Este proyecto entrena una CNN que toma una imagen RGB de un objeto y genera una reconstrucción 3D como nube de puntos (N×3).
Implementa un CNN baseline tipo PSGN (Point Set Generation Network): ResNet-18 como encoder y un MLP como
decodificador de puntos. Se entrenó y evaluó en ModelNet40, guardando artefactos de prueba (PNG/PLY/CSV)
en la carpeta `tests/`.

[**Notebook**](https://github.com/Ricardouchub/Single-Image-Reconstruccion-3D/blob/main/Single-Image%20Reconstruccion-3D%20en%20ModelNet40.ipynb) | [**Modelo**](https://github.com/Ricardouchub/Single-Image-Reconstruccion-3D/blob/main/psgn_modelnet40.pt)


<img width="1269" height="805" alt="image" src="https://github.com/user-attachments/assets/87f9f6a7-1299-43ee-ad1d-b7ad3d103c4c" />


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

Entorno
-----------------------
- Windows 10/11 con GPU NVIDIA
- Python 3.11
- PyTorch 2.6.0 + CUDA 12.4, TorchVision 0.21.x
- PyTorch3D (CUDA Toolkit 12.4 + MSVC Build Tools).
- Torchvision, open3d, trimesh, matplotlib, numpy.

Evaluación
----------
- Chamfer Distance (CD): distancia media de punto a superficie (simétrica).
- F-score@τ: con umbral τ relativo al tamaño del objeto (típicamente τ=1% del diámetro).
- Se reportan promedios y ejemplos cualitativos. En `tests/` se almacenan triptychs (input|pred|gt) y PLYs.

Limitaciones
------------
- Ambigüedad inherente de single-view (zonas ocultas). Detalle fino limitado al ser nube de puntos.
- Brecha de dominio entre renders y fotos reales.

Trabajo futuro
--------------
- Añadir Silhouette Loss estable (render diferenciable) y/o Multi-View Consistency.
- Probar AtlasNet/patch-based decoders y upsampling de puntos.
- Entrenamiento con imágenes reales + adaptación de dominio.
- Métricas por clase y tabla comparativa con ablations (N puntos, tamaño de imagen, etc.).


Autor
---------------
**Ricardo Urdaneta**
