<mxfile host="app.diagrams.net" agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:146.0) Gecko/20100101 Firefox/146.0" version="29.3.4">
  <diagram name="Page-1" id="5g0tbxIDaJLy8sxlo_d5">
    <mxGraphModel dx="1629" dy="799" grid="1" gridSize="10" guides="1" tooltips="1" connect="1" arrows="1" fold="1" page="1" pageScale="1" pageWidth="850" pageHeight="1100" math="0" shadow="0">
      <root>
        <mxCell id="0" />
        <mxCell id="1" parent="0" />
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-4" parent="1" style="ellipse;whiteSpace=wrap;html=1;aspect=fixed;" value="fw01" vertex="1">
          <mxGeometry height="80" width="80" x="270" y="260" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-8" parent="1" style="swimlane;startSize=0;" value="" vertex="1">
          <mxGeometry height="190" width="220" x="-80" y="520" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-5" parent="ic_TM0NXU4dTBpUVybGJ-8" style="shape=cylinder3;whiteSpace=wrap;html=1;boundedLbl=1;backgroundOutline=1;size=15;" value="web01" vertex="1">
          <mxGeometry height="100" width="70" x="20" y="70" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-6" parent="ic_TM0NXU4dTBpUVybGJ-8" style="shape=cylinder3;whiteSpace=wrap;html=1;boundedLbl=1;backgroundOutline=1;size=15;" value="log01" vertex="1">
          <mxGeometry height="100" width="70" x="130" y="70" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-9" parent="1" style="swimlane;startSize=0;" value="" vertex="1">
          <mxGeometry height="190" width="190" x="470" y="520" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-13" edge="1" parent="1" source="ic_TM0NXU4dTBpUVybGJ-9" style="endArrow=none;html=1;rounded=0;exitX=0.25;exitY=0;exitDx=0;exitDy=0;" target="ic_TM0NXU4dTBpUVybGJ-4" value="">
          <mxGeometry height="50" relative="1" width="50" as="geometry">
            <mxPoint x="410" y="400" as="sourcePoint" />
            <mxPoint x="460" y="350" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-14" parent="1" style="text;html=1;whiteSpace=wrap;strokeColor=none;fillColor=none;align=center;verticalAlign=middle;rounded=0;" value="eth1 LAN 172.16.150.2" vertex="1">
          <mxGeometry height="41" width="130" x="410" y="379" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-15" parent="1" style="text;html=1;whiteSpace=wrap;strokeColor=none;fillColor=none;align=center;verticalAlign=middle;rounded=0;rotation=0;" value="eth2 DMZ 172.16.50.2" vertex="1">
          <mxGeometry height="30" width="150" x="40" y="390" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-16" edge="1" parent="1" source="ic_TM0NXU4dTBpUVybGJ-8" style="endArrow=none;html=1;rounded=0;entryX=0;entryY=1;entryDx=0;entryDy=0;exitX=0.5;exitY=0;exitDx=0;exitDy=0;" target="ic_TM0NXU4dTBpUVybGJ-4" value="">
          <mxGeometry height="50" relative="1" width="50" as="geometry">
            <mxPoint x="410" y="400" as="sourcePoint" />
            <mxPoint x="460" y="350" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-17" parent="1" style="text;html=1;whiteSpace=wrap;strokeColor=none;fillColor=none;align=center;verticalAlign=middle;rounded=0;" value="eth0 WAN 10.0.17.120" vertex="1">
          <mxGeometry height="30" width="150" x="220" y="230" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-18" parent="1" style="text;html=1;whiteSpace=wrap;strokeColor=none;fillColor=none;align=center;verticalAlign=middle;rounded=0;" value="Default gateway 10.0.17.2" vertex="1">
          <mxGeometry height="30" width="150" x="20" y="70" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-1" parent="1" style="whiteSpace=wrap;html=1;aspect=fixed;" value="rw01" vertex="1">
          <mxGeometry height="80" width="80" x="525" y="250" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-20" parent="1" style="text;html=1;whiteSpace=wrap;strokeColor=none;fillColor=none;align=center;verticalAlign=middle;rounded=0;" value="SEC350-02-WAN 10.0.17.0/24" vertex="1">
          <mxGeometry height="30" width="199" x="360" y="130" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-21" edge="1" parent="1" source="ic_TM0NXU4dTBpUVybGJ-1" style="endArrow=none;html=1;rounded=0;entryX=0.5;entryY=1;entryDx=0;entryDy=0;exitX=0;exitY=0;exitDx=0;exitDy=0;" target="ic_TM0NXU4dTBpUVybGJ-20" value="">
          <mxGeometry height="50" relative="1" width="50" as="geometry">
            <mxPoint x="410" y="400" as="sourcePoint" />
            <mxPoint x="470" y="220" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-23" parent="1" style="text;html=1;whiteSpace=wrap;strokeColor=none;fillColor=none;align=center;verticalAlign=middle;rounded=0;" value="10.0.17.20" vertex="1">
          <mxGeometry height="30" width="60" x="535" y="330" as="geometry" />
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-24" edge="1" parent="1" source="ic_TM0NXU4dTBpUVybGJ-20" style="endArrow=none;html=1;rounded=0;exitX=0.25;exitY=1;exitDx=0;exitDy=0;" value="">
          <mxGeometry height="50" relative="1" width="50" as="geometry">
            <mxPoint x="410" y="400" as="sourcePoint" />
            <mxPoint x="340" y="270" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="ic_TM0NXU4dTBpUVybGJ-25" edge="1" parent="1" source="ic_TM0NXU4dTBpUVybGJ-18" style="endArrow=none;html=1;rounded=0;entryX=0.25;entryY=0;entryDx=0;entryDy=0;exitX=0.75;exitY=1;exitDx=0;exitDy=0;" target="ic_TM0NXU4dTBpUVybGJ-20" value="">
          <mxGeometry height="50" relative="1" width="50" as="geometry">
            <mxPoint x="410" y="400" as="sourcePoint" />
            <mxPoint x="460" y="350" as="targetPoint" />
          </mxGeometry>
        </mxCell>
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
