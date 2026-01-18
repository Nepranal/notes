# Proposition

> If two triangles have two angles equal to two angles, respectively, and one side equal to one side-in fact, either that by the equal angles, or that subtending one of the equal angles-then (the triangles) will also have the remaining sides equal to the [corresponding] remaining sides, and the remaining angle (equal) to the remaining angle.

Basically, given two triangles, if they have the same 2 angles and 1 side, then those two triangles are equivalent.

# Proof

![alt text](<../sources/The Elements/Screenshot 2025-12-29 at 10.25.55 AM.png>)

Say we have 2 triangles $\triangle ABC, \triangle DEF$ and that $ABC = DEF, ACB = DFE$. The whole proof will use this single diagram so it might look a little confusing

Let's start with the 1st case, and say $BC = EF$. Assume that the triangles are not equivalent i.e. $DE \neq ED, CA \neq FD, BAC \neq EDF$. Let's say $AB > DE$ (since both triangles are not the same, we're just picking the triangle with a bigger side as $\triangle ABC$).

What must be true is that $DE$ lies on the line $AB$ since it makes the same angle to $BC$ as $AB$. Since $AB > DE$, we can make a point $G$ s.t. $BG = DE$. At this stage, $\triangle BGC = \triangle DEF$ since it shares 2 sides and an angle. However, this would mean $\angle BCG = EFG < BCA$ which is a contradiction.

For the second case, let's keep the angles but now say that $AB = DE$. Similarly, we can have a point $H$ s.t. $BH = EF$. We can once again say that $\triangle BAH = \triangle EDF$ and once again draw a contradiction by comparing $AHB$ with $ACB$. These two angles must be equivalent but we know that $AHB > ACB$ since it's an external angle (reasoning from either from proposition 1.16 or proposition 1.21).
