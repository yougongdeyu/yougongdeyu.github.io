一个总的ingress 的deploycontroller，后面对应各家的service,比如bo,saw,idm.

bo套娃么，然后里面有license,ats等，但是bo对外只有bo这一个service，负责对bo所有service的汇总
认证的话是 login->ats->idm


外面怎么暴露服务，其实事aws的alb，nlb。


## smax ingress nlb alb.