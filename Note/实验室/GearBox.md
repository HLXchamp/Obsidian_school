
```java
package gearbox

port type Port()
port type PortData( int x)
port type Port2Data( int x, int y )

	connector type LinkData( PortData p1, PortData p2)
		define p1 p2
		on p1 p2 down { p1.x = p2.x; }
	end

	connector type Link2Data( Port2Data p1, Port2Data p2)
		define p1 p2
		on p1 p2 down { p1.x = p2.x;  p1.y = p2.y; }
	end

	connector type GearUp( Port2Data p1, Port2Data p2)
		define p1 p2
		on p1 p2 down {  p2.x = p2.y; if (p2.y == 6) then p2.y = 0; else p2.y = p2.y + 1; fi  }
	end

	connector type GearDown( Port2Data p1, Port2Data p2)
		define p1 p2
		on p1 p2 down { p2.x = p2.y; if (p2.y == 0) then p2.y = 6; else p2.y = p2.y - 1; fi   }
	end

connector type Link(Port p1, Port p2)
  define  p1 p2
end

connector type Link3(Port p1, Port p2, Port p3)
  define  p1 p2 p3 
end

connector type Link4(Port p1, Port p2, Port p3, Port p4)
  define  p1 p2 p3 p4
end


atom type GearBox()

  data int ErrStat = 0
  data bool Neutral = true
  data bool Idle = false

 clock x unit nanosecond

 export port Port reqset()
 export port Port gearset()
 export port Port reqneu()
 export port Port gearneu()

 port Port errorIdleNeu()
 
 place l1, l2, l3, l4, l5

  initial to l1

  on reqset
    from l1 to l2
delayable
    do {x = 0; Neutral = false;}

on errorIdleNeu
  from l2 to l5
  provided(x == 300)
do {ErrStat = 3;}

  on gearset
    from l2 to l3
    provided (x>=100 && x <300)
    delayable
    do {Idle = true;}
 
  
  on reqneu
    from l3 to l4
    delayable
    do {x = 0; Idle = false;}

on errorIdleNeu
  from l4 to l5
  provided(x == 200)
do {ErrStat = 4;}

  on gearneu
    from l4 to l1
    provided (x>=100 && x<200)
    delayable
    do {Neutral = true;}

//  invariant inv1 at l2 provided (x<=300 )
//  invariant inv2 at l4 provided (x<=200 )
end 


atom type Clutch()

  data int ErrStat = 0
  data bool Open = false
  data bool Closed = true

  clock x unit nanosecond
  export port Port openclutch()
  export port Port clutchisopen()
  export port Port clutchisclosed()
  export port Port closeclutch()
  port Port errorOpenClose()
  
  place l1, l2, l3, l4, l5
  
  initial to l1

  on openclutch
    from l1 to l2
    delayable
    do{ x = 0; Closed = false;}


  on clutchisopen
    from l2 to l3
    provided (x>=100 && x<150)
    delayable
    do { Open = true;}

  on errorOpenClose
  from l2 to l5
    provided (x==150)
  delayable
  do {ErrStat = 2;}

  on closeclutch
    from l3 to l4
    delayable
    do {x = 0; Open = false;}

  on clutchisclosed
    from l4 to l1
    provided (x>=100 && x <150)
    delayable
    do {Closed = true;}

  on errorOpenClose
  from l4 to l5
    provided (x==150)
  delayable
  do {ErrStat = 1;}

//  invariant inv1 at l2 provided (x<=150 )
//  invariant inv2 at l4 provided (x<=150 )
end

atom type Engine()
  data int ToGear = 0, FromGear = 0
  data int UseCase = 0
  data bool isError = false
data bool Initial = true
data bool Zero = false
data bool Torque = false

  clock x unit nanosecond

  export port Port2Data reqspeed(FromGear, ToGear) 
  export port Port2Data speedset(FromGear, ToGear)
  export port Port2Data reqtorque(FromGear, ToGear)
  export port Port2Data reqzerotorque(FromGear, ToGear)
  export port Port2Data torquezero(FromGear, ToGear)
 
  port Port openclutch() 
  port Port closeclutch() 
  port Port back()
  port Port errorSpeed() 
  place l1, l2, l3, l4, l5, l6, l7, l8, l9

  initial to l1

  on reqspeed 
    from l1 to l2
    delayable
    do {x = 0; UseCase = 0; Initial = false;}
  
 //internal 
  on openclutch
    from l2 to l3
    provided (x==200 )
    delayable
    do {UseCase = 2;}
  

  on speedset
    from l2 to l4
    provided (x>=50 && x<=200)
    delayable
    do {x = 0;}


  on reqtorque
    from l4 to l6
    provided (x<500 )
    delayable
    do {Torque = true;}

  on errorSpeed
    from l4 to l9
    provided (x==500 )
    delayable
    do {isError = true;}

 //internal 
  on closeclutch
    from l3 to l5
    provided( ToGear >0)
    delayable
    do {x = 0;}

  on reqtorque
    from l5 to l6
    provided (x>=50 && x<900)
    delayable
    do {Torque = true;}


  on errorSpeed
    from l5 to l9
    provided (x==900 )
    delayable
    do {isError = true;}
  
  on reqzerotorque
    from l6 to l7
    delayable
    do {x = 0; UseCase = 0; Torque = false;}

 //internal 
  on openclutch
    from l7 to l3
    provided (x==400)
    delayable
    do {UseCase = 1;}
  
  on torquezero
    from l7 to l8
    provided (x>=150 && x<=400)
    delayable
    do {Zero = true;}

 //internal 
  on back
    from l3 to l1
provided(ToGear == 0)
    delayable
    do {Initial = true;}

 //internal 
  on back
    from l8 to l1
provided(ToGear == 0)
    delayable
    do {Initial = true; Zero = false;}

  on reqspeed
    from l8 to l2
provided(ToGear > 0)
    delayable
    do {x = 0;  Zero = false;}

//  invariant inv1 at l2 provided (x<=200 )
//  invariant inv2 at l4 provided (x<=500 )
//  invariant inv3 at l5 provided (x<=900 )
//  invariant inv4 at l7 provided (x<=400 )
end

atom type GearControl()

  data int ToGear = 0, FromGear = 0
data int SysTimer = 0


data bool Gear = true
data bool Initiate = false
data bool CheckTorque = false
data bool ReqNeuGear = false
data bool CheckGearNeu = false
data bool ReqSyncSpeed = false
data bool CheckSyncSpeed = false
data bool ReqSetGear = false
data bool CheckGearSet = false
data bool ReqTorqueC = false
data bool GearChanged = false
data bool CheckClutchTwo = false
data bool ClutchOpenTwo = false
data bool CheckGearNeuTwo = false
data bool ReqSetGearTwo = false
data bool CheckClutch = false
data bool ClutchOpen = false
data bool CheckGearSetTwo = false
data bool ClutchClose = false
data bool CheckClutchClosed = false
data bool CheckClutchClosedTwo = false

  clock x unit nanosecond
  clock sysTimer unit nanosecond
  clock time_unit unit nanosecond

  
  port Port neuFromGear()
  port Port neuToGear()

  export port Port2Data reqnewgear(FromGear, ToGear)
  export port Port newgear()

  export port Port reqset()
  export port Port reqneu()
  export port Port gearset()
  export port Port gearneu()
  
  export port Port openclutch()
  export port Port closeclutch()
  export port Port clutchisopen()
  export port Port clutchisclosed()
  
  export port Port2Data reqspeed(FromGear, ToGear)
  export port Port2Data reqtorque(FromGear, ToGear)
  export port Port2Data reqzerotorque(FromGear, ToGear)
  export port Port2Data speedset(FromGear, ToGear)
  export port Port2Data torquezero(FromGear, ToGear)

  place l1, l2, l3, l4, l5, l6, l7, l8, l9, l10, l11, l12,
l13, l14, l15, l16, l17, l18, l19, l20, l21

  initial to l1 do {  time_unit' = 0; time_unit  = 1;}

  on reqnewgear
    from l1 to l2
    delayable
    do {sysTimer =0; Gear = false; Initiate = true;}
 
  on reqzerotorque 
    from l2 to l3
   provided( FromGear >0)
    delayable
    do {x = 0; Initiate = false; CheckTorque =  true;}
  
  on neuFromGear
    from l2 to l6
   provided(FromGear <=0)
    delayable
    do { Initiate = false; ReqSyncSpeed =  true;}
  
  on torquezero
    from l3 to l4
    provided (x<250 )
    delayable
    do {CheckTorque = false; ReqNeuGear = true;}


  on reqneu
    from l4 to l5
    delayable
    do {x = 0; ReqNeuGear = false; CheckGearNeu = true;}

  on gearneu
    from l5 to l6 
    provided (x <=250)
    delayable
    do {CheckGearNeu =  false; ReqSyncSpeed =  true;}

  on reqspeed
    from l6 to l7 
    //prevent deadlock
//    provided (x<=130)
 provided(ToGear > 0)
    delayable
    do {x = 0; ReqSyncSpeed =  false; CheckSyncSpeed = true;}

  //internal
  on neuToGear
    from l6 to l11 
provided(ToGear <=0)
    delayable
    do {GearChanged = true; SysTimer = sysTimer /time_unit;  ReqSyncSpeed = false;}
  
  on speedset
    from l7 to l8
    provided (x<150)
    delayable
    do {x = 0; CheckSyncSpeed = false; ReqSetGear = true;}

  on reqset
    from l8 to l9
    delayable
    //prevent deadlock
 //   provided (x<=50)
    do{x = 0; ReqSetGear = false; CheckGearSet =  true;}

  on gearset 
    from l9 to l10 
    provided (x <=350)
//    provided (x <=300)
    delayable
    do {CheckGearSet =  false; ReqTorqueC = true;}

  on reqtorque
    from l10 to l11
    delayable
    do {GearChanged = true; SysTimer = sysTimer /time_unit; ReqTorqueC = false;}
  
  on newgear
    from l11 to l1
    delayable
    do {Gear = true; GearChanged = false; }
  
  on openclutch
    from l3 to l12
    provided (x>=250 && x<=255)
    delayable
    do {x = 0; CheckTorque =  false; CheckClutchTwo =true;}
  
  on clutchisopen
    from l12 to l13
//    provided (x<=200)
    provided (x<=150)
    delayable
    do {CheckClutchTwo = false; ClutchOpenTwo = true;}

  on reqneu
    from l13 to l14
    delayable
    do {x = 0; ClutchOpenTwo =  false; CheckGearNeuTwo = true;} 

  on gearneu
    from l14 to l15
//    provided (x<=250)
    provided (x<=200)
    delayable
    do {CheckGearNeuTwo = false; ReqSetGearTwo = true;}

  on openclutch
    from l7 to l16
    provided (x>=150 && x<=155)
    delayable
    do {x = 0; CheckSyncSpeed = false; CheckClutch =  true;}

  on clutchisopen
    from l16 to l17
    provided (x<=200)
    delayable
    do {CheckClutch = false; ClutchOpen = true;}

  on reqset
    from l17 to l18
  //  provided (x<=50)
    delayable
    do {x = 0; ClutchOpen = false; CheckGearSetTwo = true;}

  on reqset 
    from l15 to l18
provided(ToGear>0)
   // provided (x<=50)
    delayable
    do {x = 0; ReqSetGearTwo = false; CheckGearSetTwo = true;}

  on closeclutch
    from l15 to l21
provided(ToGear == 0)
   // provided (x<=50)
    delayable
    do {x = 0; ReqSetGearTwo = false; CheckClutchClosedTwo = true;}

  on clutchisclosed
    from l21 to l11
//    provided (x<=200)
    provided (x<=150)
    delayable
    do {GearChanged = true; SysTimer = sysTimer /time_unit; CheckClutchClosedTwo = false;}
  
  on gearset 
    from l18 to l19
    provided (x<=350)
    delayable
    do {CheckGearSetTwo = false; ClutchClose = true;}

  on closeclutch
    from l19 to l20
   // provided (x<=50)
    delayable
    do {x = 0; ClutchClose =  false; CheckClutchClosed = true;}

  on clutchisclosed
    from l20 to l10
//    provided (x <=200)
    provided (x <=150)
    delayable
    do { ReqTorqueC =  true ; CheckClutchClosed = false;}

//  invariant inv1 at l3 provided (x<=255 )
//  invariant inv2 at l5 provided (x<=250) 
//  invariant inv3 at l7 provided (x<=155)
//  invariant inv4 at l9 provided (x<=350)
//  invariant inv5 at l12 provided (x<=200)
//  invariant inv6 at l14 provided (x<=250)
//  invariant inv7 at l16 provided (x<=200)
//  invariant inv8 at l18 provided (x<=350)
//  invariant inv9 at l20 provided (x<=200)
//  invariant inv10 at l21 provided (x<=200)
//  invariant inv11 at l6 provided (x<=130)

  //prevent deadlock
// invariant inv12 at l15 provided (x<=50)
// invariant inv13 at l17 provided (x<=50)
// invariant inv14 at l19 provided (x<=50)
// invariant inv15 at l8 provided (x<=50)
   
end


atom type Interface()
  
 data int ToGear = 0, FromGear = 0, gear = 0
 data bool N = true, stableGear = true
  
  export port Port2Data requpgear(FromGear, ToGear)
  export port Port2Data reqdowngear(FromGear, ToGear)
  export port Port newgear()

  place l1, l2, l3, l4, l5, l6, l7, l8, l9, l10, l11, l12,
l13, l14, l15, l16, l17, l18, l19


  initial to l1 

  on reqdowngear
    from l1 to l2
    delayable
    do { N = false; FromGear = 0; ToGear = 6; stableGear = false;}
  
  on newgear
    from l2 to l3
    delayable
    do {gear = -1; stableGear = true;}

  on requpgear
    from l3 to l4
    delayable
    do {FromGear = 6; ToGear = 0; stableGear = false;}
  
  on newgear
    from l4 to l1
    delayable
    do {N = true; gear = 0; stableGear = true;}

  on requpgear
    from l1 to l5
    delayable
    do {  N = false; FromGear = 0; ToGear = 1; stableGear = false;}
  
  on newgear
    from l5 to l6
    delayable
    do { gear = 1;  stableGear = true;}

  on reqdowngear
    from l6 to l7
    delayable
    do {FromGear = 1; ToGear = 0; stableGear = false;}
  
  on newgear
    from l7 to l1
    delayable
    do {N = true; gear = 0; stableGear = true;}

  on requpgear
    from l6 to l8
    delayable
    do {FromGear = 1; ToGear = 2; stableGear = false;}
  
  on newgear
    from l8 to l9
    delayable
    do { gear = 2; stableGear = true;}

  on reqdowngear
    from l9 to l10
    delayable
    do {FromGear = 2; ToGear = 1; stableGear = false;}
  
  on newgear
    from l10 to l6
    delayable
    do {gear = 1; stableGear = true;}

  on requpgear
    from l9 to l11
    delayable
    do {FromGear = 2; ToGear = 3; stableGear = false;}
  
  on newgear
    from l11 to l12
    delayable
    do {gear  = 3; stableGear = true;}

  on reqdowngear
    from l12 to l13
    delayable
    do {FromGear = 3; ToGear = 2; stableGear = false;}
  
  on newgear
    from l13 to l9
    delayable
    do { gear = 2; stableGear = true;}

  on requpgear
    from l12 to l14
    delayable
    do {FromGear = 3; ToGear = 4; stableGear = false;}
  
  on newgear
    from l14 to l15
    delayable
    do {gear = 4; stableGear = true;}

  on reqdowngear
    from l15 to l16
    delayable
    do {FromGear = 4; ToGear = 3; stableGear = false;}
  
  on newgear
    from l16 to l12
    delayable
    do {gear = 3; stableGear = true;}

  on requpgear
    from l15 to l17
    delayable
    do {FromGear = 4; ToGear = 5; stableGear = false;}
  
  on newgear
    from l17 to l18
    delayable
    do {gear = 5; stableGear = true;}

  on reqdowngear
    from l18 to l19
    delayable
    do {FromGear = 5; ToGear = 4; stableGear = false;}
  
  on newgear
    from l19 to l15
    delayable
    do {gear = 4; stableGear = true;}
end

compound type Compound()

  component GearBox gb()
  component Clutch c()
  component Engine e()
  component GearControl gc()
  component Interface inf()
  

  //IF-GC 
//  connector Link reqnewgear(inf.reqnewgear, gc.reqnewgear)

  connector GearUp newUpGear(inf.requpgear,gc.reqnewgear)
  connector GearDown newDownGear(inf.reqdowngear,gc.reqnewgear)
  connector Link newgear(inf.newgear, gc.newgear)
  
  //GC-Clutch
  connector Link openclutch(c.openclutch, gc.openclutch)
  connector Link clutchisopen(c.clutchisopen, gc.clutchisopen)
  connector Link closeclutch(c.closeclutch, gc.closeclutch)
  connector Link clutchisclosed(c.clutchisclosed, gc.clutchisclosed)
  
  //GC-GB
  connector Link reqset(gb.reqset, gc.reqset)
  connector Link gearset(gb.gearset, gc.gearset)
  connector Link reqneu(gb.reqneu, gc.reqneu)
  connector Link gearneu(gb.gearneu, gc.gearneu)
  //GC-E
  connector Link2Data reqspeed(e.reqspeed, gc.reqspeed)
  connector Link2Data reqtorque(e.reqtorque, gc.reqtorque)
  connector Link2Data torquezero(e.torquezero, gc.torquezero)
  connector Link2Data speedset(e.speedset, gc.speedset)
  connector Link2Data reqzerotorque(e.reqzerotorque, gc.reqzerotorque)
  


end
  
end
```

Interface组件：

```java
atom type Interface()
  
 data int ToGear = 0, FromGear = 0, gear = 0
 data bool N = true, stableGear = true
  
  export port Port2Data requpgear(FromGear, ToGear)
  export port Port2Data reqdowngear(FromGear, ToGear)
  export port Port newgear()

  place l1, l2, l3, l4, l5, l6, l7, l8, l9, l10, l11, l12,
l13, l14, l15, l16, l17, l18, l19


  initial to l1 

  on reqdowngear
    from l1 to l2
    delayable
    do { N = false; FromGear = 0; ToGear = 6; stableGear = false;}
  
  on newgear
    from l2 to l3
    delayable
    do {gear = -1; stableGear = true;}

  on requpgear
    from l3 to l4
    delayable
    do {FromGear = 6; ToGear = 0; stableGear = false;}
  
  on newgear
    from l4 to l1
    delayable
    do {N = true; gear = 0; stableGear = true;}

  on requpgear
    from l1 to l5
    delayable
    do {  N = false; FromGear = 0; ToGear = 1; stableGear = false;}
  
  on newgear
    from l5 to l6
    delayable
    do { gear = 1;  stableGear = true;}

  on reqdowngear
    from l6 to l7
    delayable
    do {FromGear = 1; ToGear = 0; stableGear = false;}
  
  on newgear
    from l7 to l1
    delayable
    do {N = true; gear = 0; stableGear = true;}

  on requpgear
    from l6 to l8
    delayable
    do {FromGear = 1; ToGear = 2; stableGear = false;}
  
  on newgear
    from l8 to l9
    delayable
    do { gear = 2; stableGear = true;}

  on reqdowngear
    from l9 to l10
    delayable
    do {FromGear = 2; ToGear = 1; stableGear = false;}
  
  on newgear
    from l10 to l6
    delayable
    do {gear = 1; stableGear = true;}

  on requpgear
    from l9 to l11
    delayable
    do {FromGear = 2; ToGear = 3; stableGear = false;}
  
  on newgear
    from l11 to l12
    delayable
    do {gear  = 3; stableGear = true;}

  on reqdowngear
    from l12 to l13
    delayable
    do {FromGear = 3; ToGear = 2; stableGear = false;}
  
  on newgear
    from l13 to l9
    delayable
    do { gear = 2; stableGear = true;}

  on requpgear
    from l12 to l14
    delayable
    do {FromGear = 3; ToGear = 4; stableGear = false;}
  
  on newgear
    from l14 to l15
    delayable
    do {gear = 4; stableGear = true;}

  on reqdowngear
    from l15 to l16
    delayable
    do {FromGear = 4; ToGear = 3; stableGear = false;}
  
  on newgear
    from l16 to l12
    delayable
    do {gear = 3; stableGear = true;}

  on requpgear
    from l15 to l17
    delayable
    do {FromGear = 4; ToGear = 5; stableGear = false;}
  
  on newgear
    from l17 to l18
    delayable
    do {gear = 5; stableGear = true;}

  on reqdowngear
    from l18 to l19
    delayable
    do {FromGear = 5; ToGear = 4; stableGear = false;}
  
  on newgear
    from l19 to l15
    delayable
    do {gear = 4; stableGear = true;}
end
```

 GearBox组件：
 
```java
atom type GearBox()

  data int ErrStat = 0
  data bool Neutral = true
  data bool Idle = false

 clock x unit nanosecond

 export port Port reqset()
 export port Port gearset()
 export port Port reqneu()
 export port Port gearneu()

 port Port errorIdleNeu()
 
 place l1, l2, l3, l4, l5

  initial to l1

  on reqset
    from l1 to l2
delayable
    do {x = 0; Neutral = false;}

on errorIdleNeu
  from l2 to l5
  provided(x == 300)
do {ErrStat = 3;}

  on gearset
    from l2 to l3
    provided (x>=100 && x <300)
    delayable
    do {Idle = true;}
 
  
  on reqneu
    from l3 to l4
    delayable
    do {x = 0; Idle = false;}

on errorIdleNeu
  from l4 to l5
  provided(x == 200)
do {ErrStat = 4;}

  on gearneu
    from l4 to l1
    provided (x>=100 && x<200)
    delayable
    do {Neutral = true;}

//  invariant inv1 at l2 provided (x<=300 )
//  invariant inv2 at l4 provided (x<=200 )
end 
```

