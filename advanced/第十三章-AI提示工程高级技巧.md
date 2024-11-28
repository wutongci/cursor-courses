[返回目录](./README.md) | [上一章：部署配置驱动开发](./第十二章-部署配置驱动开发.md) | [下一章：总结-AI辅助开发高级实践](./总结-AI辅助开发高级实践.md)

# 第十三章：AI提示工程高级技巧

## 理论基础

AI提示工程（Prompt Engineering）是一门将需求准确转化为AI可理解指令的技术。掌握高级提示技巧可以显著提高AI辅助开发的效率和质量。

## 核心概念

1. 提示组成要素
   - 上下文（Context）
   - 指令（Instruction）
   - 示例（Example）
   - 输出格式（Output Format）

2. 提示策略
   - 链式思考（Chain of Thought）
   - 思维树（Tree of Thoughts）
   - 角色扮演（Role Playing）
   - 逐步引导（Step by Step）

## 实战案例：复杂功能开发

### 案例1：多步骤代码生成

```python
# 提示策略示例：
"""
角色：你是一位专业的Python后端开发工程师
任务：实现一个文件处理服务
要求：
1. 支持多种文件格式（PDF、Excel、Word）
2. 实现文件解析和数据提取
3. 添加错误处理和日志记录
4. 考虑性能优化

请按以下步骤实现：
1. 首先定义接口和数据模型
2. 然后实现核心处理逻辑
3. 最后添加错误处理和日志
"""

from abc import ABC, abstractmethod
from typing import Dict, Any
import logging
from pathlib import Path

# 1. 定义文件处理器接口
class FileProcessor(ABC):
    @abstractmethod
    def process(self, file_path: Path) -> Dict[str, Any]:
        """处理文件并返回提取的数据"""
        pass

# 2. 实现具体的处理器
class PDFProcessor(FileProcessor):
    def process(self, file_path: Path) -> Dict[str, Any]:
        try:
            logging.info(f"开始处理PDF文件: {file_path}")
            # PDF处理逻辑
            return {"status": "success", "data": {...}}
        except Exception as e:
            logging.error(f"PDF处理错误: {str(e)}")
            raise

class ExcelProcessor(FileProcessor):
    def process(self, file_path: Path) -> Dict[str, Any]:
        try:
            logging.info(f"开始处理Excel文件: {file_path}")
            # Excel处理逻辑
            return {"status": "success", "data": {...}}
        except Exception as e:
            logging.error(f"Excel处理错误: {str(e)}")
            raise

# 3. 文件处理服务
class FileProcessingService:
    def __init__(self):
        self.processors = {
            ".pdf": PDFProcessor(),
            ".xlsx": ExcelProcessor(),
            # 添加更多处理器
        }
        self._setup_logging()
    
    def _setup_logging(self):
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
        )
    
    def process_file(self, file_path: str) -> Dict[str, Any]:
        path = Path(file_path)
        if not path.exists():
            raise FileNotFoundError(f"文件不存在: {file_path}")
            
        processor = self.processors.get(path.suffix.lower())
        if not processor:
            raise ValueError(f"不支持的文件类型: {path.suffix}")
            
        return processor.process(path)
```

### 案例2：高级重构提示

```python
# 提示策略示例：
"""
角色：你是一位代码重构专家
任务：重构遗留代码系统
上下文：
- 这是一个老旧的订单处理系统
- 代码耦合度高，难以维护
- 需要提高代码的可测试性

请按以下步骤进行重构：
1. 识别代码气味
2. 应用SOLID原则
3. 引入设计模式
4. 添加单元测试
"""

# 重构前的代码
class OldOrderSystem:
    def process_order(self, order_data):
        # 处理订单、支付、库存、通知等逻辑全部混在一起
        pass

# 重构后的代码
from abc import ABC, abstractmethod
from typing import List

# 1. 定义接口
class OrderProcessor(ABC):
    @abstractmethod
    def process(self, order: Order) -> bool:
        pass

class PaymentProcessor(ABC):
    @abstractmethod
    def process_payment(self, order: Order) -> bool:
        pass

class InventoryManager(ABC):
    @abstractmethod
    def update_inventory(self, order: Order) -> bool:
        pass

# 2. 实现具体类
class OrderService:
    def __init__(
        self,
        payment_processor: PaymentProcessor,
        inventory_manager: InventoryManager,
        notification_service: NotificationService
    ):
        self.payment_processor = payment_processor
        self.inventory_manager = inventory_manager
        self.notification_service = notification_service
    
    def process_order(self, order: Order) -> bool:
        try:
            # 使用事务管理器确保操作的原子性
            with TransactionManager() as tx:
                if not self.payment_processor.process_payment(order):
                    return False
                    
                if not self.inventory_manager.update_inventory(order):
                    return False
                    
                self.notification_service.notify_customer(order)
                return True
                
        except Exception as e:
            logging.error(f"订单处理失败: {str(e)}")
            return False
```

### 案例3：性能优化提示

```python
# 提示策略示例：
"""
角色：你是一位性能优化专家
任务：优化数据处理管道
上下文：
- 处理大量用户行为数据
- 当前处理速度慢
- 内存使用过高

请从以下方面进行优化：
1. 算法复杂度
2. 内存使用
3. 并发处理
4. 缓存策略
"""

from typing import Iterator, List
from concurrent.futures import ThreadPoolExecutor
from functools import lru_cache
import pandas as pd

class DataProcessor:
    def __init__(self, batch_size: int = 1000):
        self.batch_size = batch_size
        self.cache = {}
    
    def process_data(self, data_iterator: Iterator) -> List[dict]:
        # 1. 批量处理
        batches = self._create_batches(data_iterator)
        
        # 2. 并行处理
        with ThreadPoolExecutor() as executor:
            results = list(executor.map(self._process_batch, batches))
            
        # 3. 合并结果
        return [item for batch in results for item in batch]
    
    def _create_batches(self, iterator: Iterator) -> List[List]:
        """将数据分批处理，减少内存使用"""
        batch = []
        for item in iterator:
            batch.append(item)
            if len(batch) >= self.batch_size:
                yield batch
                batch = []
        if batch:
            yield batch
    
    @lru_cache(maxsize=1000)
    def _process_item(self, item: dict) -> dict:
        """处理单个数据项，使用缓存避免重复计算"""
        # 处理逻辑
        return processed_item
    
    def _process_batch(self, batch: List) -> List[dict]:
        """处理数据批次"""
        return [self._process_item(item) for item in batch]
```

## 最佳实践

1. 提示结构化
   - 明确角色和任务
   - 提供完整上下文
   - 分步骤引导
   - 指定输出格式

2. 示例驱动
   - 提供具体示例
   - 说明预期结果
   - 解释关键决策

3. 迭代优化
   - 根据输出调整提示
   - 增加约束条件
   - 细化要求

## 注意事项

1. 上下文管理
   - 控制信息量
   - 突出重点
   - 保持相关性

2. 提示清晰度
   - 避免歧义
   - 使用专业术语
   - 保持一致性

3. 输出控制
   - 指定格式要求
   - 设置质量标准
   - 处理异常情况

## 小结

1. 提示工程是AI辅助开发的核心技能
2. 好的提示需要结构化和系统化
3. 通过实践和迭代来优化提示策略
4. 合理运用高级技巧可以显著提高开发效率

---

[返回目录](./README.md) | [上一章：部署配置驱动开发](./第十二章-部署配置驱动开发.md) | [下一章：总结-AI辅助开发高级实践](./总结-AI辅助开发高级实践.md)
