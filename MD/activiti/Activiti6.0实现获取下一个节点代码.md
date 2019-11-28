### Activiti6.0实现获取下一个节点代码

```java
package com.fanhuaholdings.cbs;

import com.fanhuaholdings.cbs.activiti.entity.ActRuTask;
import com.fanhuaholdings.cbs.activiti.entity.ActRuVariable;
import de.odysseus.el.ExpressionFactoryImpl;
import de.odysseus.el.util.SimpleContext;
import org.activiti.bpmn.model.*;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.util.StringUtils;

import javax.el.ExpressionFactory;
import javax.el.ValueExpression;
import java.util.ArrayList;
import java.util.Collection;
import java.util.List;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class ActivitiGetNextNode {

    /**
     * 获得下一个节点的审批人或者节点名字
     *
     * @param processId 流程实例id
     * @return
     * @throws Exception
     */
    @Test
    public void nextProcessApprover() throws Exception {

        String processId = "6152501";

        //获得流程当前所处位置
        List<ActRuTask> actRuTasks = actRuTaskRepositoryJPA.getTaskByProcessId(processId);

        //根据流程id获得流程模式id
        String processDefinitionId = historyService.createHistoricProcessInstanceQuery()
                .processInstanceId(processId).singleResult().getProcessDefinitionId();
        //获得流程模型
        BpmnModel model = repositoryService.getBpmnModel(processDefinitionId);

        //最终usertask节点
        List<FlowElement> userTasks = new ArrayList<>();
        if (model != null && actRuTasks != null && actRuTasks.size() != 0) {
            //获得流程模型的所有节点
            Collection<FlowElement> flowElements = model.getMainProcess().getFlowElements();
            //遍历节点
            for (FlowElement e : flowElements) {
                for (ActRuTask task : actRuTasks) {
                    String currentKey = task.getTaskDefKey();
                    if (!StringUtils.isEmpty(currentKey)) {
                        //获得当前节点
                        if (e.getId().equals(currentKey)) {
                            if (e instanceof UserTask) {
                                List<SequenceFlow> currentOutgoingFlows = ((UserTask) e).getOutgoingFlows();
                                //获得流程中的所有变量信息
                                List<ActRuVariable> actRuVariables = actRuVariableRepositoryJPA.getVariableByProcessId(processId);
                                for (SequenceFlow s : currentOutgoingFlows) {
                                    nextNode(flowElements, s, actRuVariables, userTasks);
                                }
                            }
                        }
                    }
                }

            }
        }

        StringBuffer sb = new StringBuffer();
        for (FlowElement e : userTasks) {
            if (e instanceof UserTask) {
                System.out.println(e.getName());
                e.getName();

            } else if (e instanceof EndEvent) {
                System.out.println(e.getName());
                sb.append(e.getName());
            }
        }

    }

    /**
     * @param flowElements        该模型的所有节点
     * @param currentSequenceFlow 当前流程线
     * @param elKV                所有变量信息
     * @param userTasks           最终结果集
     * @throws Exception
     */
    private void nextNode(Collection<FlowElement> flowElements, SequenceFlow currentSequenceFlow, List<ActRuVariable> elKV, List<FlowElement> userTasks) throws Exception {
        //获得当前线在
        String SequenceFlowId = currentSequenceFlow.getTargetRef();
        SequenceFlow defaultFlow = null;

        for (FlowElement e : flowElements) {
            if (e.getId().equals(SequenceFlowId)) {
                //判断类型
                //如果是排他网管
                if (e instanceof ExclusiveGateway) {
                    //默认流程线
                    String defaultFlowString = ((ExclusiveGateway) e).getDefaultFlow();
                    List<SequenceFlow> egSequenceFlow = ((ExclusiveGateway) e).getOutgoingFlows();
                    //标识
                    boolean boo = true;
                    for (int i = 0; i < egSequenceFlow.size(); i++) {
                        if (egSequenceFlow.get(i).getId().equals(defaultFlowString)) {
                            defaultFlow = egSequenceFlow.get(i);
                        }
                        if (!StringUtils.isEmpty(egSequenceFlow.get(i).getConditionExpression())) {
                            //判断el选择路线
                            if (isCondition(egSequenceFlow.get(i).getConditionExpression(), elKV)) {
                                boo = false;
                                //如果为真说明会走这条路线  递归
                                nextNode(flowElements, egSequenceFlow.get(i), elKV, userTasks);
                            }
                        }

                        //如果最后一个走完没有el为true的，则查看是否有默认流程，如果没有抛出异常
                        if (i == egSequenceFlow.size() - 1 && boo) {
                            if (StringUtils.isEmpty(defaultFlowString)) {
                                throw new Exception("流程异常");
                            } else {
                                //如果有默认流程 递归
                                nextNode(flowElements, defaultFlow, elKV, userTasks);
                            }
                        }
                    }
                    //如果是user用户审批节点
                } else if (e instanceof UserTask) {
                    userTasks.add(e);
                    e.getId();
                } else if (e instanceof EndEvent) {
                    userTasks.add(e);
                }
            }
        }

    }

    //判断el表达式
    private boolean isCondition(String el, List<ActRuVariable> elKV) {
        ExpressionFactory factory = new ExpressionFactoryImpl();
        SimpleContext context = new SimpleContext();
        for (int i = 0; i < elKV.size(); i++) {
            context.setVariable(elKV.get(i).getName(), factory.createValueExpression(elKV.get(i).getText(), String.class));
        }
        ValueExpression e = factory.createValueExpression(context, el, boolean.class);
        return (Boolean) e.getValue(context);
    }
}

```

