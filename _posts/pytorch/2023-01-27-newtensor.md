---
published: True
title:  "[Pytorch] tensor.new_tensor 사용법"

categories:
  - pytorch
tags:
  - [pytorch, tensor]
 
date: 2023-01-27
last_modified_at: 2023-01-27
---

코드 분석을 하던 중 tensor.new_tensor() 라는 tensor 조작 함수가 나와 앞으로 알면 좋을 것 같아 포스팅 하게 되었다.  


아래 링크는 new_tensor()에 대한 document이다.  
<https://pytorch.org/docs/stable/generated/torch.Tensor.new_tensor.html>  
  
  
사용법: Tensor.new_tensor(data, *, dtype=None, device=None, requires_grad=False, layout=torch.strided, pin_memory=False) → Tensor  

이 document에서 확인할 수 있다시피 어떤 list가 들어오면 이를 tensor 형식으로 변환해주는 함수이다.  

이때, data에는 임의의 list가 올 수 있고, Tensor에는 data를 tensor 형식으로 변환할 때 내가 원하는 dtype의 tensor 이름을 넣어주면 된다.  
  
ex)  
```
>>> data = [[0, 1], [2, 3]]
>>> aa = torch.ones((2,), dtype=torch.int8)
>>> aa.new_tensor(data) # data를 aa와 같은 타입을 갖는 tensor로 변환함. --> torch.int8
tensor([[0, 1],
        [2, 3]], dtype=torch.int8)
>>> aa = torch.ones((2,), dtype=torch.float16) # --> torch.float16
>>> aa.new_tensor(data)
tensor([[0., 1.],
        [2., 3.]], dtype=torch.float16)
```

document에서는 x.clone().detach()을 사용할 것을 추천한다.  

나 역시 x.clone().detach()을 자주 사용했는데, new_tensor도 알아두면 좋을 듯 하다!  

다만 내가 분석하는 코드에서 new_tensor()를 사용한 특별한 이유가 있는지는 아직 모르겠다..



