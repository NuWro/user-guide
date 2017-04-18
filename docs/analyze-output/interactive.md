You can analyze NuWro output in the *ROOT* interactive mode.
To use variables and methods described in [basics section](basics)
you need to load a proper dictionary. It is as simple as running
ROOT through `myroot` located in `nuwro/bin`.

!!! hint ""
    Just type `myroot` if you have `nuwro/bin` added to your `$PATH`
    
First you need to load a *ROOT* file and point to the right *TTree*:

    root [0] TFile* file = new TFile ("/path/to/output.root")
    root [1] TTree* tree = (TTree*)file->Get("treeout")
    
You can easily draw any distribution for variables described
in [basics section](basics) using *Draw* function.

!!! note "Example"
    To draw Q2 distribution simply type:

        root [2] tree->Draw("-q2()")
    
    which access `event->q2()`.
    
    ![](/img/examples/interactive/Q2.svg)
    
!!! note "Example"
    To draw target nucleon (`in[1]`) momentum distribution:
    
        root [3] tree->Draw("in[1].momentum()")
        
    which access `event->particle->momentum()`.
        
    ![](/img/examples/interactive/nucleon_momentum.svg)
    
!!! note "Example"
    To draw outgoing lepton momentum *z* component:
    
        root [4] tree->Draw("out[0].p().z")
        
    which access `event->particle->vec->z`.
        
    ![](/img/examples/interactive/lepton_mom_z.svg)
    
!!! note "Example"
    You can add cuts by providing boolean expression as a second argument of
    *Draw* function:
    
        root [5] tree->Draw("-q2()", "out[0].Ek() > 500")
        
    which draws Q2 distribution for events with outgoing lepton kinetic energy
    above 500 MeV.

    ![](/img/examples/interactive/Q2_cut.svg)

