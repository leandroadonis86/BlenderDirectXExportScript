# BlenderDirectXExportScript
Adapted\Modified Exporter script for Blender 2.69 to export models to (.x) extension DirectX 8, 9, ++ Development.

**BlenderDirectXExportScript: Directx 8 9 .x with ExportVertexDuplicates for Blender 2.69 r60995**

Adapted\Modified Exporter script for Blender 2.69 to export models (.x) extension DirectX 8, 9, ++ Development.
**Tested on:** Windows XP SP3 with Blender 2.69, full compactible with Directx 8.1 SDK for VB6 (dx8vb.dll).
This script was adapted and modified from 2 other scripts **Arben (Ben) Omari** and **Chris Foster** links below.

**From __init__.py:**

*Added VertexDuplicationIndices export option:*
```
    ExportVertexDuplicates = BoolProperty(
        name="    Export Vertex Duplicates",
        description="Export mesh vertex duplicates. Reduce duplicates "\
            "face vertices on a mesh, optimize build model code.",
        default=False)
```
**From export_x.py:**

*Added template VertexDuplicationIndices header:*
```
        if self.Config.ExportVertexDuplicates:
            self.File.Write("template VertexDuplicationIndices {\n")
            self.File.Write(" <b8d65549-d7c9-4995-89cf-53a9a8b031e3>\n")
            self.File.Write(" DWORD nIndices;\n")
            self.File.Write(" DWORD nOriginalVertices;\n")
            self.File.Write(" array DWORD indices[nIndices];\n}\n\n")
```
*Adapted Config options:*
```
        if self.Config.ExportVertexDuplicates:
            self.Exporter.Log("Writing VertexDuplicationIndices...")
            self.__WriteMeshVertexDuplicates(Mesh)
            self.Exporter.Log("Done")
```
*Added def __WriteMeshVertexDuplicates function:*
```
    def __WriteMeshVertexDuplicates(self, Mesh):
        numvert = 0
        numunique = 0
        vert_index = {}
        unique_index_list = []
        
        current_obj = bpy.context.active_object
        
        for face in current_obj.data.polygons:
            verts_in_face = face.vertices[:]
            for vert in verts_in_face:
                
                vec_vert = Vector([current_obj.data.vertices[vert].co[0], current_obj.data.vertices[vert].co[1], current_obj.data.vertices[vert].co[2], 1])
                f_vec_vert = vec_vert
                
                key = (str(round(f_vec_vert[0], 4)), str(round(f_vec_vert[1], 4)), str(round(f_vec_vert[2], 4)))
                
                if key not in vert_index:
                    vert_index[key] = numvert
                    numunique += 1
                
                numvert += 1
                unique_index_list.append(vert_index[key])
        
        self.Exporter.File.Write("VertexDuplicationIndices {\n")
        self.Exporter.File.Write("  %d;\n" % (numvert))
        self.Exporter.File.Write("  %d;\n" % (numunique))
        
        n = 0
        for ind in unique_index_list:
            n += 1
            if n == numvert:
                delimeter = ";"
            else:
                delimeter = ","
            
            self.Exporter.File.Write("  %d%s\n" % (ind, delimeter))
        
        self.Exporter.File.Write("} //End of VertexDuplicationIndices\n")
```

**Setup:**
- Place "io_scene_x" in folder "..\Blender Foundation\Blender\2.69\scripts\addons"
- Run Blender 2.69
- Menu "File" > "User preferences..." > (Tab) "Addons" > "Import-Export: DirectX .x Format" ...
 > (check it) > (button) "Save User Settings"

**Export Options Added:**
- Export Vertex Duplicates

**Source Based from:**
*Vertex Duplicates from previous Blender 2.4v (**Arben (Ben) Omari**):*
https://github.com/andygeers/directx-exporter/blob/master/DirectXExporter_Geero.py

*Main autor Directx Exporter Script (**Chris Foster**):*
https://github.com/bwrsandman/blender-addons/blob/master/io_scene_x/export_x.py
