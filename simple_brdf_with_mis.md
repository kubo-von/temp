//PSEUDO CODE
{
closure(Radiance, Dir){
	omega_i = worldToTangent(Dir);
	omega_m = (omega_o + omega_i ).normalize();
	o_dot_h = omega_o.dot(omega_m);
	fresnel = fresnel::fresnel_dielectric(o_dot_h, 1.0, ior);
	specular_weight = lerp(0.04, 0.4, metallic); //
  specular_weight = specular_weight+(1.0-specular_weight)*fresnel;
	diffuse_weight = lerp((1.0 - specular_weight), 0.0, metallic)
	spec_color = lerp(white,albedo,metallic);
	
	return:
	 lambert::f(N,Dir) * radiance * diffuse_weight * albedo 
	+ggx::f(omega_m, omega_i, omega_o, roughness) * radiance * specular_weight * spec_color
}

omega_o = worldToTangent(viewerDirection)

light = choseLightToSample();
LightRadiance, LightSampleDir, LightSamplePdf = light.eval()
LightSamplePdf /= totalNumberOfLights
LightDirect = closure(RayResult.Radiance,bsdfSampleDir)

rnd_lobe = rng.get1D()
rnd_sample = rng.get2D()
if rnd_lobe < 0.5 :
	bsdfSampleDir = lambert::sample(rnd_sample)
else:
	bsdfSampleDir = ggx::sample(rnd_sample,roughness)

omega_i_bsdf = worldToTangent(bsdfSampleDir)
bsdfSamplePdf = 
( lambert::pdf(omega_i_bsdf) 
+ ggx::pdf(omega_i_bsdf,omega_o,roughness) )/2.0

Indirect = Zero
LightBsdf = Zero
RayResult = traceIndirectLight(bsdfSampleDir)
if RayResult.hit:
	if RayResult.hit.geometry.isLight(){
		LightBsdf = closure(RayResult.Radiance,bsdfSampleDir)
	else:
		Indirect = closure(RayResult.Radiance,bsdfSampleDir)


MisWeight = BalceHeuristics(LightSamplePdf,bsdfSamplePdf)
Direct = LightDirect*MisWeight + LightBsdf*(1-MisWeight)

return Direct + Indirect
}






