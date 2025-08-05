# Quantum Well LED Simulation using Silvaco ATLAS

**Military Institute of Science & Technology**  
Department of Electrical Electronic and Communication Engineering  

**Course:** Optoelectronics [EECE-459]  
**Assignment:** Demonstration of the basic structure and working principle of a Quantum Well (QW) LED using Silvaco ATLAS.  

**Submitted by:**  
1. Anindya Chanda Tirtha — ID: 202216009  
2. Md. Raisul Islam Ratul — ID: 202216049  

**Instructor:** Lec Muhammed Zubair Rahman  
**Submission Date:** 05 August 2025  

---

## 1. Introduction
The purpose of this assignment is to simulate a **Quantum Well (QW) Light Emitting Diode (LED)** using the Silvaco ATLAS TCAD tool, based on an **Al₀.₃Ga₀.₇As/GaAs** heterostructure.  
The aim is to:
- Design the device with group-specific parameters.
- Simulate electrical and optical characteristics.
- Compare peak emission wavelength with theoretical expectations.

---

## 2. Objectives
- Design a single quantum well LED using **Al₀.₃Ga₀.₇As** and **GaAs**.
- Simulate the **I–V characteristics** and identify turn-on voltage.
- Simulate the **optical emission spectrum**.
- Determine the **peak emission wavelength**.
- Compare simulated results with **theoretical values**.

---

## 3. Device Structure & Parameters

| Parameter | Value |
|-----------|-------|
| Quantum Well Material | GaAs |
| Barrier Material | Al₀.₃Ga₀.₇As |
| Quantum Well Thickness | 9 nm |
| Barrier Thickness | 45 nm |
| N-region Doping | GaAs, n-type, 1e18 cm⁻³ |
| P-region Doping | GaAs, p-type, 1e19 cm⁻³ |

Mesh was refined near quantum well and barrier interfaces to improve simulation accuracy.

---

## 4. Workflow & Code Explanation

1. **Mesh Definition**  
   Fine vertical mesh near the active region for accurate resolution of the quantum well and barriers.

2. **Layer Setup**  
   - 9 nm GaAs quantum well  
   - 45 nm Al₀.₃Ga₀.₇As barriers on both sides

3. **Electrodes & Doping**  
   Top and bottom contacts with optimized doping for carrier injection.

4. **Model Settings**  
   Enable strain, polarization, recombination (SRH, Auger), optical transitions, and **k·p** quantum effects.

5. **Simulation**  
   - I–V sweep: 0 V → 3.0 V  
   - Jump to 4.0 V for optical analysis

6. **Optical Spectrum**  
   Simulated electroluminescence in **GaAs IR range (~870 nm)**.

---

## 5. ATLAS Simulation Code

```atlas
go atlas

mesh width=1e8
x.mesh loc=0.0 spac=0.5
x.mesh loc=1.0 spac=0.5
y.mesh loc=-5.00 spac=1.0
y.mesh loc=-0.70 spac=0.005
y.mesh loc=-0.655 spac=0.001
y.mesh loc=-0.645 spac=0.0002
y.mesh loc=-0.636 spac=0.0002
y.mesh loc=-0.626 spac=0.001
y.mesh loc=-0.58 spac=0.005
y.mesh loc=-0.0 spac=0.1

region num=1 material=GaAs    y.max=-0.70
region num=2 material=AlGaAs  y.min=-0.70  y.max=-0.645 x.comp=0.3
region num=3 material=GaAs    y.min=-0.645 y.max=-0.636 name=well qwell led well.ny=50
region num=4 material=AlGaAs  y.min=-0.636 y.max=-0.591 x.comp=0.3
region num=5 material=GaAs    y.min=-0.591

electrode name=anode   bottom
electrode name=cathode top

doping region=1 uniform n.type conc=1e18
doping region=5 uniform p.type conc=1e19

models calc.strain polarization polar.scale=1.0
models fermi incomplete consrh auger optr print k.p
models region=3 k.p chuang spontaneous lorentz

material material=GaAs taun0=1e-9 taup0=1e-9 copt=1.1e-8 augn=1e-30 augp=1e-30
material material=AlGaAs taun0=1e-9 taup0=1e-9 copt=1.1e-8 augn=1e-30 augp=1e-30

material well.gamma0=30e-3
material edb=0.080 eab=0.101
mobility mun0=100 mup0=10 

output con.band val.band band.param charge polar.charge e.mobility h.mobility
method climit=1e-4 maxtrap=10

solve init
solve prev
save outf=led_start.str

log outf=led_iv.log

probe name=Radiative integrate radiative rname=well

solve vstep=0.05 vfinal=3.0 name=anode
save outf=led_3p0.str

solve name=anode vfinal=4.0
save spectrum=led_el_4p0.spc lmin=0.75 lmax=0.95 nsamp=300
save outf=led_4p0.str

tonyplot led_iv.log
tonyplot led_el_4p0.spc

quit
