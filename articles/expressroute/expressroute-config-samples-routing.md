<properties
   pageTitle="Express 경로 고객 라우터 구성 샘플 | Microsoft Azure"
   description="이 페이지는 Cisco 및 Juniper 라우터에 대한 라우터 구성 샘플을 제공합니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# 라우팅 설정 및 관리를 위한 라우터 구성 샘플

이 페이지는 Cisco IOS-XE 및 Juniper MX 시리즈 라우터에 대한 인터페이스 및 라우팅 구성 샘플을 제공합니다. 이러한 샘플은 참조용이므로 그대로 사용해서는 안 됩니다. 사용 중인 네트워크에 적절하게 구성하려면 공급업체와 작업하면 됩니다.

>[AZURE.IMPORTANT]이 페이지에 있는 샘플은 참조용입니다. 공급업체의 영업/기술 팀 및 네트워킹 팀과 함께 작업하면서 필요에 맞게 적절히 구성해야 합니다. Microsoft는 이 페이지에 나열된 구성과 관련된 문제를 지원하지 않습니다. 지원 문제는 장치 공급업체에 문의해야 합니다.

아래의 라우터 구성 샘플은 모든 피어링에 적용됩니다. 라우팅에 대한 자세한 내용은 [Express 경로 피어링](expressroute-circuit-peerings.md) 및 [Express 경로 라우팅 요구 사항](expressroute-routing.md)을 검토하세요.

## Cisco IOS-XE 기반 라우터

이 섹션의 샘플은 IOS-XE OS 제품군을 실행하는 모든 라우터에 적용됩니다.

### 1\. 인터페이스 및 하위 인터페이스 구성

Microsoft에 연결하는 모든 라우터에서 피어링별로 하위 인터페이스가 필요합니다. 하위 인터페이스는 VLAN ID 또는 누적된 한 쌍의 VLAN ID 및 IP 주소로 식별될 수 있습니다.

#### Dot1Q 인터페이스 정의

이 샘플에서는 VLAN ID가 하나인 하위 인터페이스에 대한 하위 인터페이스 정의를 제공합니다. VLAN ID는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### QinQ 인터페이스 정의

이 샘플에서는 VLAN ID가 두 개인 하위 인터페이스에 대한 하위 인터페이스 정의를 제공합니다. 바깥쪽의 VLAN ID(s-tag)는 사용되는 경우 모든 피어링에서 동일하게 유지됩니다. 안쪽의 VLAN ID(c-tag)는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### 2\. eBGP 세션 설정

모든 피어링에 대해 Microsoft와 BGP 세션을 설정해야 합니다. 아래의 샘플에서 Microsoft와 BGP 세션을 설정할 수 있습니다. 하위 인터페이스에 사용한 IPv4 주소가 a.b.c.d인 경우 BGP 인접 라우터(Microsoft)의 IP 주소는 a.b.c.d+1입니다. BGP 인접 라우터에 대한 IPv4 주소의 마지막 옥텟은 항상 짝수입니다.

	router bgp <Customer_ASN>
	 bgp log-neighbor-changes
	 neighbor <IP#2_used_by_Azure> remote-as 12076
	 !        
	 address-family ipv4
	 neighbor <IP#2_used_by_Azure> activate
	 exit-address-family
	!

### 3\. BGP 세션을 통해 알릴 접두사 설정

선택된 접두사를 Microsoft에 알리도록 라우터를 구성할 수 있습니다. 아래의 샘플을 사용하여 이 작업을 수행할 수 있습니다.

	router bgp <Customer_ASN>
	 bgp log-neighbor-changes
	 neighbor <IP#2_used_by_Azure> remote-as 12076
	 !        
	 address-family ipv4
	  network <Prefix_to_be_advertised> mask <Subnet_mask>
	  neighbor <IP#2_used_by_Azure> activate
	 exit-address-family
	!

### 4\. 경로 맵

경로 맵과 접두사 목록을 사용하여 네트워크에 전파되는 접두사를 필터링할 수 있습니다. 아래의 샘플을 사용하여 이 작업을 수행할 수 있습니다. 적절한 접두사 목록이 설정되어 있어야 합니다.

	router bgp <Customer_ASN>
	 bgp log-neighbor-changes
	 neighbor <IP#2_used_by_Azure> remote-as 12076
	 !        
	 address-family ipv4
	  network <Prefix_to_be_advertised> mask <Subnet_mask>
	  neighbor <IP#2_used_by_Azure> activate
	  neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
	 exit-address-family
	!
	route-map <MS_Prefixes_Inbound> permit 10
	 match ip address prefix-list <MS_Prefixes>
	!


## Juniper MX 시리즈 라우터 

이 섹션의 샘플은 모든 Juniper MX 시리즈 라우터에 적용됩니다.

### 1\. 인터페이스 및 하위 인터페이스 구성

#### Dot1Q 인터페이스 정의

이 샘플에서는 VLAN ID가 하나인 하위 인터페이스에 대한 하위 인터페이스 정의를 제공합니다. VLAN ID는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

    interfaces {
    	vlan-tagging;
    	<Interface_Number> {
    		unit <Number> {
    			vlan-id <VLAN_ID>;
    			family inet {
    				address <IPv4_Address/Subnet_Mask>;
    			}
    		}
    	}
    }


#### QinQ 인터페이스 정의

이 샘플에서는 VLAN ID가 두 개인 하위 인터페이스에 대한 하위 인터페이스 정의를 제공합니다. 바깥쪽의 VLAN ID(s-tag)는 사용되는 경우 모든 피어링에서 동일하게 유지됩니다. 안쪽의 VLAN ID(c-tag)는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

	interfaces {
	    <Interface_Number> {
	        flexible-vlan-tagging;
	        unit <Number> {
	            vlan-tags outer <S-tag> inner <C-tag>;
	            family inet {
	                address <IPv4_Address/Subnet_Mask>;
	            }                           
	        }                               
	    }                                   
	}                           

### 2\. eBGP 세션 설정

모든 피어링에 대해 Microsoft와 BGP 세션을 설정해야 합니다. 아래의 샘플에서 Microsoft와 BGP 세션을 설정할 수 있습니다. 하위 인터페이스에 사용한 IPv4 주소가 a.b.c.d인 경우 BGP 인접 라우터(Microsoft)의 IP 주소는 a.b.c.d+1입니다. BGP 인접 라우터에 대한 IPv4 주소의 마지막 옥텟은 항상 짝수입니다.

	routing-options {
	    autonomous-system <Customer_ASN>;
	}
	}
	protocols {
	    bgp { 
	        group <Group_Name> { 
	            peer-as 12076;              
	            neighbor <IP#2_used_by_Azure>;
	        }                               
	    }                                   
	}

### 3\. BGP 세션을 통해 알릴 접두사 설정

선택된 접두사를 Microsoft에 알리도록 라우터를 구성할 수 있습니다. 아래의 샘플을 사용하여 이 작업을 수행할 수 있습니다.

	policy-options {
	    policy-statement <Policy_Name> {
	        term 1 {
	            from protocol OSPF;
		route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
	            then {
	                accept;
	            }
	        }
	    }
	}
	protocols {
	    bgp { 
	        group <Group_Name> { 
	            export <Policy_Name>
	            peer-as 12076;              
	            neighbor <IP#2_used_by_Azure>;
	        }                               
	    }                                   
	}


### 4\. 경로 맵

경로 맵과 접두사 목록을 사용하여 네트워크에 전파되는 접두사를 필터링할 수 있습니다. 아래의 샘플을 사용하여 이 작업을 수행할 수 있습니다. 적절한 접두사 목록이 설정되어 있어야 합니다.

	policy-options {
	    prefix-list MS_Prefixes {
	        <IP_Prefix_1/Subnet_Mask>;
	        <IP_Prefix_2/Subnet_Mask>;
	    }
	    policy-statement <MS_Prefixes_Inbound> {
	        term 1 {
	            from {
		prefix-list MS_Prefixes;
	            }
	            then {
	                accept;
	            }
	        }
	    }
	}
	protocols {
	    bgp { 
	        group <Group_Name> { 
	            export <Policy_Name>
	            import <MS_Prefixes_Inbound>
	            peer-as 12076;              
	            neighbor <IP#2_used_by_Azure>;
	        }                               
	    }                                   
	}

## 다음 단계

자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.

<!---HONumber=AcomDC_0121_2016-->